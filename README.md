# k8s-self-hosted-runners

Kubernetes Community Days Guatemala - Self-hosted GitHub Actions runners using Kubernetes.

1. Create GitHub organization - `kcd-guatemala`.
2. Create repo in org as k8s-self-hosted-runners into new organization.
3. Push code to new repo adding new remote.

    ```sh
        git remote set-url origin git@github.com:kcd-guatemala/k8s-self-hosted-runners.git
        git remote set-url --push origin git@github.com:kcd-guatemala/k8s-self-hosted-runners.git
        git branch -M main
        git push -u origin main
    ```

4. Launch `hello-world` GitHub Action on GitHub cloud manually.
5. Create GitHub Personal Access Token with following permissions:
    - `repo` (full access)
    - `workflow` (full control)
    - `admin:org` (full access)
    - `admin:public_key` (read: public_key)
    - `admin:repo_hook` (read:repo_hook)
    - `admin:org_hook` (full control)
    - `notifications` (full control)

6. Clone new repository and open it in VS Code.
7. Create a local kind cluster in your laptop.

    ```sh
        kind create cluster
    ```

8.  Install the [cert-manager Helm chart](https://cert-manager.io/docs/installation/helm/).

    ```sh
        helm repo add jetstack https://charts.jetstack.io

        helm upgrade --install \
        cert-manager jetstack/cert-manager \
        --namespace cert-manager \
        --create-namespace \
        --version v1.6.1 \
        --set installCRDs=true
    ```

9. Install the [actions-runner-controller Helm chart](https://github.com/actions-runner-controller/actions-runner-controller).
  
    ```sh
        helm repo add actions-runner-controller \
        https://actions-runner-controller.github.io/actions-runner-controller

        helm upgrade --install \
        actions-runner-controller \
        actions-runner-controller/actions-runner-controller \
        --namespace actions-runner-system \
        --create-namespace \
        --set authSecret.create=true \
        --set authSecret.github_token=<TOKEN>
    ```

10. Create the repository runner.

    ```sh
        kubectl apply -f runners/repo-runner.yaml
    ```

11. Modify the `runs-on` field of the `hello-world` GitHub Action to `self-hosted`.
12. Launch `hello-world` GitHub Action which runs inside kind cluster.
13. Delete the repository runner.
14. Trigger the `hello-world` GitHub Action. Will be enqueued and waiting for a runner.
15. Create the runner to use in the existing repository again and show how the action is running in the runner again.
16. Create another repository with a README and create and trigger the `hello-world` GitHub Action. Will be enqueued and waiting for a runner.
17. Delete the repository runner and create the organization runner.

    ```sh
        kubectl apply -f runners/org-runner.yaml
    ```

18. Both actions will be running in the organization runner.
19. Comment enterprise runner.
20. Delete organization runner and create the deployment runner for the organization.

    ```sh
        kubectl apply -f runners/deployment-runner.yaml
    ```

21. Create the HRA - more available options in the docs. Remove replicas from the deployment runner.

    ```sh
        kubectl apply -f runners/hra.yaml
    ```

22. Comment runner labels.
23. Comment runner groups for enterprise plan.
24. Stateful Runners.
25. Usage limits.
26. Billing minutes.

## References

- https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners
- https://docs.github.com/en/actions/hosting-your-own-runners/about-self-hosted-runners#usage-limits
- https://docs.github.com/en/billing/managing-billing-for-github-actions/about-billing-for-github-actions#included-storage-and-minutes
- https://github.com/actions-runner-controller/actions-runner-controller