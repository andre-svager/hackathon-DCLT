
FROM golang:1.26-alpine3.24 AS builder
 
WORKDIR /app
 
RUN apk add --no-cache git
 
# go.mod / go.sum live under app/, not at the repo root
COPY app/go.mod app/go.sum ./
RUN go mod download
 
# Rest of the Go source, also under app/
COPY app/ .
 
# init.sql is a sibling of app/, so it needs its own copy from context root
COPY db/init.sql ./db/init.sql
 
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -ldflags="-s -w" -o donation-service .
 
# ---------- Runtime ----------
FROM gcr.io/distroless/static:nonroot-amd64
 
WORKDIR /app
 
COPY --from=builder /app/donation-service /app/donation-service
COPY --from=builder /app/db/init.sql /app/db/init.sql
 
EXPOSE 8082
 
USER nonroot:nonroot
 
CMD ["/app/donation-service"]