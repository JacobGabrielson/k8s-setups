FROM golang:1.14 AS build

WORKDIR /output

RUN go get -tags netgo -ldflags '-w -extldflags "-static"'  -u github.com/etcd-io/etcd
RUN go get -tags netgo -ldflags '-w -extldflags "-static"'  -u github.com/etcd-io/etcd/etcdctl
RUN cp $GOPATH/bin/etcd* .

RUN go get -u github.com/cloudflare/cfssl/cmd/cfssl
RUN go get -u github.com/cloudflare/cfssl/cmd/cfssljson
RUN curl -s -o kubectl -L https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/arm64/kubectl
RUN curl -s -o kube-apiserver -L https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/arm64/kube-apiserver
RUN curl -s -o kube-controller-manager -L https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/arm64/kube-controller-manager
RUN curl -s -o kube-scheduler -L https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/arm64/kube-scheduler
RUN curl -s -o kube-proxy -L https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/arm64/kube-proxy
RUN curl -s -o kubelet -L https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/arm64/kubelet


RUN curl -s -o - -L https://github.com/kubernetes-sigs/cri-tools/releases/download/v1.15.0/crictl-v1.15.0-linux-arm64.tar.gz | tar -zxf - crictl
RUN curl -s -o - -L https://github.com/containernetworking/plugins/releases/download/v0.8.2/cni-plugins-linux-arm64-v0.8.2.tgz | tar -zxf -

RUN chmod +x kube*

COPY ./pki-script /pki-script

ENV PATH="/output:${PATH}"

RUN /pki-script/certificate-authority
RUN /pki-script/kubernetes-configuration-files
RUN /pki-script/data-encryption-keys
RUN ls -ltr

FROM ubuntu:20.04
RUN apt-get update && apt-get install -y ruby-mustache rsync

COPY --from=build /output /output
COPY ./build-script /build-script

COPY ./overlays /overlays
WORKDIR /generated-overlays

RUN mkdir -p worker-0
RUN /build-script/build-overlay /overlays/worker worker-0 /overlays/substitutions/worker-0.yaml /output/worker-0 /output
RUN ls -lR worker-0/

#RUN mkdir -p controller-0
#RUN /build-script/build-overlay /overlays/controller controller-0 controller-0 10.200.0.0/24 /output
#RUN ls -lR controller-0

