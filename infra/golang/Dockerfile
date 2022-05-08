FROM golang:1.18.1-alpine as dev

# set envs
ARG TZ=Asia/Tokyo
ENV TZ ${TZ}
ENV LANG C.UTF-8
ENV WORKSPACE=/go/src/app
ENV CGO_ENABLED 0
COPY ./${APP_DIR} $WORKSPACE
WORKDIR $WORKSPACE

RUN apk update && apk add git
COPY ./${APP_DIR}/go.mod ./${APP_DIR}/go.sum ./
RUN go mod download
EXPOSE $APP_PORT

CMD ["go", "run", "main.go"]

FROM golang:1.18.1-alpine as builder

ENV WORKSPACE=/go/src/app
WORKDIR ${WORKSPACE}

RUN apk update && apk add git
COPY go.mod go.sum ./
RUN go mod download

COPY . ${WORKSPACE}
RUN CGO_ENABLED=0 GOOS=linux go build -o $ROOT/binary


FROM scratch as prod

ENV WORKSPACE=/go/src/app
WORKDIR ${WORKSPACE}
COPY --from=builder ${WORKSPACE}/binary ${WORKSPACE}

EXPOSE 8080
CMD ["/go/src/app/binary"]