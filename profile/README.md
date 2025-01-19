# Home Lab

The lab is a collection of applications and services that are used to demonstrate and test various technologies and methodologies. The lab is built using GitOps principles and practices, with the goal of providing a consistent and reliable environment for development and testing.

## Repositories Structure

- [app] Repository

  Contains the source code for applications, including any workflows. Tags and releases are created here for production deployments. PRs labeled appropriately trigger preview deployments via Argo CD. See [argo-config](https://github.com/dudo-home-lab/argo-config/blob/main/app-of-apps/apps/) for more information.

- [app]-deployment Repository

  Holds deployment manifests and configuration files for the application. These repositories are auto discovered by Argo CD for continuous deployment to their applicable environments. See [argo-config](https://github.com/dudo-home-lab/argo-config/blob/main/app-of-apps/apps/) for more information.

- [[tool]-addon](https://github.com/dudo-home-lab/gateway-api-addon) Repository

  Manages Kubernetes addons and configurations that enhance a cluster's functionality. These repositories are auto discovered by Argo CD for continuous deployment to all clusters. See [argo-config](https://github.com/dudo-home-lab/argo-config/blob/main/app-of-apps/addons/) for more information.

- [.github](https://github.com/dudo-home-lab/.github) Repository

  This is a special repository within GitHub that provides functionality across the organization.

  - [Customizing Organization Profile](https://docs.github.com/en/organizations/collaborating-with-groups-in-organizations/customizing-your-organizations-profile)
  - [Creating Default Community Health Files](https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/creating-a-default-community-health-file)
  - [GitHub Action Workflow Templates](https://docs.github.com/en/actions/sharing-automations/creating-workflow-templates-for-your-organization)
  - [Reusing GitHub Action Workflows](https://docs.github.com/en/actions/sharing-automations/reusing-workflows)

- [.github-private](https://github.com/dudo-home-lab/.github-private) Repository

  This is another special repository within GitHub that [extends profile customization](https://docs.github.com/en/organizations/collaborating-with-groups-in-organizations/customizing-your-organizations-profile#adding-a-member-only-organization-profile-readme).
