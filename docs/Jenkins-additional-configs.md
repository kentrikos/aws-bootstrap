# Jenkins custom configs

Custom configs could be added on top of Kentrikos base configuration using plugin [JCasC](https://github.com/jenkinsci/configuration-as-code-plugin#getting-started)

## Adding Configs

New configs as described in [link](https://github.com/kentrikos/terraform-aws-bootstrap-jenkins) are added from directory configured as parameter `jenkins_additional_jcasc`

### Example

1. In ```operations/<region>/jenkins-core-infra``` create dir jcasc
2. Create JCasC files in dir
3. Edit jenkins.tf and add ```jenkins_additional_jcasc``` param

```hcl-terraform
module "jenkins" {
  source = "github.com/kentrikos/terraform-aws-bootstrap-jenkins?ref=0.8.0" //version minimal
//  ...
  jenkins_additional_jcasc = "${path.cwd}/jcasc/"
  }
```

## Adding Jobs

Jobs are managed by [JobDSL](https://wiki.jenkins.io/display/JENKINS/Job+DSL+Plugin) plugin with JCasC plugin.

### Example

Useful [examples](https://github.com/jenkinsci/configuration-as-code-plugin/tree/master/demos/jobs) and [kentrikos](https://github.com/kentrikos/terraform-aws-bootstrap-jenkins/blob/master/jenkins/jenkins.yaml.tpl#L58) implementation
