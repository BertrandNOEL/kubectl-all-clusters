# kubectl-all-clusters

## Intro

This tool executes a `kubectl` command on multiple Kubernetes clusters in one execution.

It is very handy if you have multiple clusters (dev, pre, qa, etc...), and need to run a command on all of them in one go.

It does not change the context state of your local laptop. Meaning that if you were running `kubectl` command in one cluster before using it, you will still target this cluster after/during execution.

## Installation

```bash
git clone git@github.com:BertrandNOEL/kubectl-all-clusters.git
sudo ln -s -f $PWD/kubectl-all-clusters/kubectl-all-clusters /usr/local/bin/kubectl-all-clusters
sudo ln -s -f $PWD/kubectl-all-clusters/kubectl-all-clusters /usr/local/bin/kac
```

## Configuration

First, you need to define the clusters you want to talk to. For now, you need to modify the script, the `CLUSTERS` variable. Example of one config:
```
CLUSTERS=(
"minikube;default;Local laptop deployment"
"arn:aws:eks:us-east-1:123456:cluster/dev-cluster;dev-myteam;Dev cluster for my team"
"arn:aws:eks:us-east-1:123456:cluster/dev-cluster;dev-otherteam;Dev cluster other team"
"arn:aws:eks:us-east-1:678910:cluster/qa-cluster;services;QA"
"arn:aws:eks:eu-west-2:111213:cluster/pre-cluster;services;Pre"
)
```
The format is the following. Each field separated by `;`
```
"$kubectl_context;$kubectl_namespace;$my_pretty_name"
```

## Use

Type your command as if you would be using `kubectl`, but replace `kubectl` by `kubectl-all-clusters` (or `kac`)

Example:
```bash
kac get deployments
```

```
Local laptop deployment
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           1d
myApp   1/1     1            1           1d

Dev cluster for my team
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           2d
myApp   1/1     1            1           4d

Dev cluster other team
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
otherApp   1/1     1            1           1d

QA
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
nginx      1/1     1            1           1d
myApp      1/1     1            1           4d
otherApp   1/1     1            1           1d

Pre
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
nginx      4/4     4            4           10d
myApp      2/2     2            2           7d
otherApp   3/3     3            3           8d
```

You can as well grep the output. Example:
```bash
kac get deployment/myApp | grep Image
```

```
Local laptop deployment
    Image:       123456.dkr.ecr.us-east-1.amazonaws.com/myApp:1.3.0-74bbfc5586

Dev cluster for my team
    Image:       123456.dkr.ecr.us-east-1.amazonaws.com/myApp:1.2.0-568766b5f6

Dev cluster other team

QA
    Image:       123456.dkr.ecr.us-east-1.amazonaws.com/myApp:1.1.0

Pre
    Image:       123456.dkr.ecr.us-east-1.amazonaws.com/myApp:1.0.0
```

## TODO

- [ ] Export clusters config to a proper config file
