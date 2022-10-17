FROM golang:1.18 as one
RUN sleep 10 && echo "one reporing $(date)" > /tmp/one
FROM golang:1.18 as two
RUN echo "two reporing in $(date)" > /tmp/two

FROM ubuntu as main
COPY --from=one /tmp/one /tmp/one
COPY --from=two /tmp/two /tmp/two

CMD ["cat", "/tmp/one", "/tmp/two"]