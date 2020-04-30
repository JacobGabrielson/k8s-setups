FROM golang:1.14

WORKDIR /output

RUN go get -tags netgo -ldflags '-w -extldflags "-static"'  -u github.com/etcd-io/etcd
RUN go get -tags netgo -ldflags '-w -extldflags "-static"'  -u github.com/etcd-io/etcd/etcdctl
RUN go get -u github.com/cloudflare/cfssl/cmd/cfssl
RUN go get -u github.com/cloudflare/cfssl/cmd/cfssljson
RUN curl -s -o /usr/local/bin/kubectl -L https://storage.googleapis.com/kubernetes-release/release/v1.15.3/bin/linux/arm64/kubectl
RUN chmod +x /usr/local/bin/kubectl

COPY ./script /script

RUN /script/certificate-authority
