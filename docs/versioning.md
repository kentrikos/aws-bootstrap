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

New changes should be developed and tested using features branches (checkout from master). Dependencies should be upgraded by tagging repositories in bottom to top manner.

### Branching

* Master branch is for stable, tested changes.
* Features are developing on separate branches that ara checkout from master

## Deployment

This repository (aws-bootstrap) is considered top-level repository for Kentrikos project and its version is bound to required versions of all other dependent repositories as described in REQUIREMENTS file as repo:version per line
eg.
Giving provided below  structure of dependency, change in terraform-aws-kops module must be updated and tagged from bottom to top level repo. 
```
aws-bootstrap
|
 \template-environment-configuration
  |
   \template-environment-configuration
    |
     \terraform-aws-kops
```

### How to deploy from latest stable version

1. Take last version from master branch of this repository (aws-bootstrap) 
1. Follow instructions on "Environment configuration" section. 

### How to deploy from latest development version

1. Take last stable version of Kentrikos
1. In configuration repo update selected module to desired branch or tag
1. Apply changes to environment

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

To update environment configuration we should base on desired version of Kentrikos project.

1. From aws-bootstrap repo we choose proper version.
1. From REQUIREMENTS we take required version of template-environment-configuration repo.
1. Check changes against last version of private template-environment-configuration repo.
1. Apply changes to private environment configuration repo.