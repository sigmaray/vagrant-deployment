# Build stage
FROM golang:1.21-alpine AS builder

# Устанавливаем необходимые инструменты для CGO (SQLite требует CGO)
RUN apk add --no-cache gcc musl-dev

WORKDIR /app

# Копируем файлы зависимостей
COPY go.mod go.sum ./
RUN go mod download

# Копируем исходный код
COPY main.go ./

# Собираем приложение
# Добавляем флаги для совместимости с musl (Alpine Linux)
RUN CGO_ENABLED=1 GOOS=linux CGO_CFLAGS="-D_LARGEFILE64_SOURCE" go build -a -installsuffix cgo -o jokes-api .

# Production stage
FROM alpine:latest

# Устанавливаем необходимые библиотеки для SQLite и wget для healthcheck
RUN apk --no-cache add ca-certificates sqlite wget

WORKDIR /app

# Копируем бинарник из builder stage
COPY --from=builder /app/jokes-api .

# Создаем директорию для базы данных
RUN mkdir -p /app/data

# Указываем порт
EXPOSE 8100

# Запускаем приложение
CMD ["./jokes-api"]

