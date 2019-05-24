# Jobs order

## Install/Update order

| LP | Folder | Name | Account | TF path |
|:---:|:------------:|:----|:----|:-----|
| 1 | Infrastructure | Install EKS in Operations account | Operations | `operations/<region>/env-eks` |
| 2 | Infrastructure | Install EKS in Application account | Application | `application/<region>/env-eks` |
| 3 | LMA | Create Ingress in Operations Account | Operations |  `operations/<region>/env-eks`  |
| 4 | LMA | Deploy Grafana in Operations Account | Operations | `operations/<region>/env-eks`  |
| 5 | LMA | Deploy Prometheus in Operations Account | Operations | `operations/<region>/env-eks` |
| 6 | LMA | Deploy Prometheus in Application Account | Application | `application/<region>/env-eks` |
| 7 | LMA | Create Logging in Operations Account | Operations | `operations/<region>/logging` |
| 8 | LMA | Create Logging in Application Account | Application |  `application/<region>/logging`|

## Remove order

| LP | Folder | Name | Account | TF path |
|:---:|:------------:|:----|:----|:-----|
| 1 | LMA | Remove Logging in Application Account | Application |  `application/<region>/logging`|
| 2 | LMA | Remove Logging in Operations Account | Operations | `operations/<region>/logging` |
| 3 | LMA | Remove Prometheus in Application Account | Application | `application/<region>/env-eks` |
| 4 | LMA | Remove Prometheus in Operations Account | Operations | `operations/<region>/env-eks` |
| 5 | LMA | Remove Grafana in Operations Account | Operations |  `operations/<region>/env-eks` |
| 6 | LMA | Remove Ingress in Operations Account | Operations | `operations/<region>/env-eks` |
| 7 | Infrastructure | Remove EKS in Application account | Application | `application/<region>/env-eks` |
| 8 | Infrastructure | Remove EKS in Operations account | Operations | `operations/<region>/env-eks` |