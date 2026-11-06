# The public mirror

The public repository [`renable-com/agent-plugin`](https://github.com/renable-com/agent-plugin) is a verbatim copy
of the `plugins/` folder of Renable's source repository: its root is this folder. Nobody edits it by hand and no
agent pushes to it. Pull requests opened there are not merged: the change is made in the source repository and
reaches the mirror on its own.

## How a change reaches it

The workflow `.github/workflows/mirror-agent-plugin.yml` in the source repository runs after the source
repository's CI has passed for a push to `master`, and on demand from `master`; it refuses every other branch. It
copies `plugins/` over the mirror's `main` branch and pushes ONE commit, `Mirror of plugins/ at <source commit>`,
when anything changed. The mirror never receives the source repository's history or commit messages. It pushes
with a deploy key that has write access to the mirror only, held as the secret `AGENT_PLUGIN_DEPLOY_KEY` of the
source repository's GitHub environment `agent-plugin-mirror`, which only `master` may deploy to: no branch or pull
request workflow can read the key. Without that secret the workflow ends with a notice and pushes nothing.

## Setting it up (once, by a person with admin rights on both repositories)

Run from a shell signed in to GitHub (`gh auth status`), in an empty scratch directory outside any repository.
`SOURCE` is the source repository's `owner/name`:

```sh
SOURCE=<owner/name of Renable's source repository>
```

1. Create the public repository, empty (no README, so the first mirror commit is its first commit):

   ```sh
   gh repo create renable-com/agent-plugin --public \
     --description "Renable's plugins for AI agents, mirrored from Renable's source repository"
   ```

2. Create the environment in the source repository, deployable from `master` only:

   ```sh
   gh api -X PUT "repos/$SOURCE/environments/agent-plugin-mirror" --input - <<'EOF'
   {"deployment_branch_policy": {"protected_branches": false, "custom_branch_policies": true}}
   EOF
   gh api -X POST "repos/$SOURCE/environments/agent-plugin-mirror/deployment-branch-policies" \
     -f name=master -f type=branch
   ```

3. Generate the deploy key pair, with no passphrase (the workflow cannot type one):

   ```sh
   ssh-keygen -t ed25519 -N "" -C "agent-plugin mirror" -f agent-plugin-deploy-key
   ```

4. Give the public half write access to the mirror, and store the private half as the environment's secret (never
   as a repository secret, which every branch's workflows can read):

   ```sh
   gh repo deploy-key add agent-plugin-deploy-key.pub --repo renable-com/agent-plugin \
     --title "Mirror from Renable's source repository" --allow-write
   gh secret set AGENT_PLUGIN_DEPLOY_KEY --repo "$SOURCE" --env agent-plugin-mirror < agent-plugin-deploy-key
   ```

   A deploy key added through `gh` is tied to the token that added it: revoking that token removes the key, and
   the next mirror run then fails at the push. Adding the key in the repository's Settings > Deploy keys instead
   avoids that.

5. Delete both key files: `rm agent-plugin-deploy-key agent-plugin-deploy-key.pub`.

6. Run the first mirror. The workflow must already be on `master`:

   ```sh
   gh workflow run mirror-agent-plugin.yml --repo "$SOURCE" --ref master
   gh run list --workflow mirror-agent-plugin.yml --repo "$SOURCE" --limit 1
   gh run watch <run id from the line above> --repo "$SOURCE"
   ```

   After that, every push to `master` whose CI passes mirrors on its own, and pushes nothing when `plugins/` did
   not change.

7. Verify that the mirror equals the folder, from a checkout of the source repository's `master`:

   ```sh
   git clone https://github.com/renable-com/agent-plugin.git ../agent-plugin-check
   diff -r --exclude=.git ../agent-plugin-check plugins && echo "mirror matches"
   claude plugin validate ../agent-plugin-check
   ```

   `diff` prints nothing and `claude plugin validate` ends with `Validation passed`. A person can then add the
   marketplace with `claude plugin marketplace add renable-com/agent-plugin` and install the Claude plugin with
   `claude plugin install renable@renable`.
