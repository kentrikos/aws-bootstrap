# Versioning

Aim of this document is to describe versioning practices in [Kentrikos](https://github.com/kentrikos) project.

## SemVer 2.0

In all project repositories we use Semantic Versioning 2.0 <https://semver.org/> 

## Summary 

This is summary from <https://semver.org/spec/v2.0.0.html> about naming convention:

```
Given a version number MAJOR.MINOR.PATCH, increment the:

    1. MAJOR version when you make incompatible API changes,
    2. MINOR version when you add functionality in a backwards-compatible manner, and
    3. PATCH version when you make backwards-compatible bug fixes.

Additional labels for pre-release and build metadata are available as extensions to the MAJOR.MINOR.PATCH format.

```

## Tagging

Git tags MUST follow MAJOR.MINOR.PATCH convention eg. 0.0.1, 0.1.0, 1.0.0  on master branch.
Git tags on non master branches (features to master) should add -rc -alfa -beta if needed for major changes.
Git tags for patches for older releases could be done on stable/fix branch eg. 1.2.3

## Developing 

New changes are developed on branch from master. Testing changes from dependent modules/repos are done on separate branch. To upgrade dependency first that repos should be tagged and than upgraded in dependant repo.

## Deployment

Version of Kentrkios project is based on this repo version. All other dependencies as an requirement in REQUIREMENTS file as repo and version. 

## Terraform 

Updating terraform module version:
Example:

```hcl-terraform
//from
module "jenkins" {
  source = "github.com/kentrikos/terraform-aws-bootstrap-jenkins?ref=0.1.0"

  //...
}
//to

module "jenkins" {
  source = "github.com/kentrikos/terraform-aws-bootstrap-jenkins?ref=0.2.0"

  //...
}
```

## Environment configuration

To update environment configuration we should base on desire version of Kentrikos project.

1. From aws-bootsrap repo we chouse proper version
1. From REQUIREMENTS we take depended version of template-environment-configuration repo
1. Check changes from our last version of template-environment-configuration and selected.
1. Apply changes to private environment configuration repo.