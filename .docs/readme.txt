docker run --name postgres12 -e POSTGRES_USER=root -e POSTGRES_PASSWORD=secret -p 5432:5432 -d postgres:12-alpine
docker exec -ti postgres12 psql -U postgres -W
docker start postgres12

# install golang-migrate
yay -S migrate

mkdir -p db/migration

migrate create -ext sql -dir db/migration -seq init_schema

docker exec -ti postgres12 /bin/sh
createdb --username=root --owner=root simple_bank


# migrate
migrate -path db/migration -database "postgresql://root:secret@localhost:5432/simple_bank?sslmode=disable" -verbose up
migrate -path db/migration -database "postgresql://root:secret@localhost:5432/simple_bank?sslmode=disable" -verbose down

# install sqlc
yay -S sqlc
sqlc init
sqlc generate
