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

RUN chmod +x kube*

COPY ./script /script

ENV PATH="/output:${PATH}"

RUN /script/certificate-authority
RUN /script/kubernetes-configuration-files
RUN /script/data-encryption-keys
RUN ls -ltr

FROM ubuntu:20.04
RUN apt-get update && apt-get install -y ruby-mustache rsync

COPY --from=build /output /output
COPY --from=build /script /script

COPY ./overlays /overlays
WORKDIR /generated-overlays

RUN mkdir -p worker-0
RUN /script/build-overlay /overlays/worker worker-0 worker-0 10.200.0.0/24
RUN ls -lR worker-0/



