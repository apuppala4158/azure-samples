## AKS + Linkerd V2 ##

**Get Linkerd CLI and setup your environment**

    LINKERD_VERSION=stable-2.7.1
    curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
    sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
    sudo chmod +x /usr/local/bin/linkerd

**Generate the bash completion file and source it in your current shell**

    mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
    source ~/completions/linkerd.bash

**Source the bash completion file in your .bashrc so that the command-line completions are permanently available in your shell**

    echo "source ~/completions/linkerd.bash" >> ~/.bashrc

**Validate Linkerd version**

    linkerd version
        Client version: stable-2.7.1
        Server version: unavailable

**Install Linkerd Control Plane**

    linkerd check --pre
    linkerd install | kubectl apply -f -
    linkerd check
    linkerd version
    kubectl -n linkerd get deployments
    kubectl -n linkerd get services
    kubectl -n linkerd get pods
    linkerd dashboard &

**Verify admission webhook is enabled**

    kubectl -n linkerd get deploy -l linkerd.io/control-plane-component=proxy-injector

**Enable automatic injection of linkerd sidecar through namespace annotation**

    apiVersion: v1
    kind: Namespace
    metadata:
        name: demo
        annotations:
            linkerd.io/inject: enabled

**If needed enable linkerd sidecar injection in already created namespace and/ or deployment**

    kubectl get ns <namespacename> -o yaml | linkerd inject - | kubectl apply -f -
    kubectl get deploy -o yaml -n <namespace> | linkerd inject - | kubectl apply -f -

**Install sample booksapp**

    curl -sL https://run.linkerd.io/booksapp.yml | kubectl -n demo apply -f -

**Check deployment & pod stats**

    linkerd -n demo stat deployments
    linkerd -n demo stat pods

**Access sample booksapp**

    kubectl -n demo port-forward svc/webapp 7000 &

****