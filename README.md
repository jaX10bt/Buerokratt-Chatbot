# Bürokratt's Chat Bot

# Scope

This repo will primarily contain:

1. Architectural and other documentation;
2. Docker Compose file to set up and run Bürokratt's Chat Bot as a fully functional service;

## Dev setup

- Clone [Ruuter](https://github.com/buerokratt/Ruuter)
- For Apple Silicon, replace Ruuter's Dockerfile line and add platform specification `FROM --platform=linux/amd64 openjdk:17-jdk-alpine`
- Ruuter has an unresolved issue with allowing cross-origin credentials to be sent, for now fix this by adding:
  `.allowCredentials(true);` to line 24 in CORSConfiguration.java
- Navigate to Ruuter and build the image `docker build -t ruuter .`
- Clone [Resql](https://github.com/buerokratt/Resql)
- Navigate to Resql and build the image `docker build -t resql .`
- Clone [Data Mapper](https://github.com/buerokratt/DataMapper)
- Navigate to Data Mapper and build the image `docker build -t datamapper-node .`
- Clone [TIM](https://github.com/buerokratt/TIM)
- Go to src -> main -> resources -> application.properties & modify `security.allowlist.jwt` value to `security.allowlist.jwt=ruuter-v1-public,ruuter-v1-private,ruuter-v2-private,ruuter-v2-public,dmapper,resql,tim,tim-postgresql,chat-widget,customer-service,127.0.0.1,::1`
- Go to src -> main -> resources -> application.properties & add `auth.success.redirect.whitelist=http://localhost:8085,http://localhost:8085/auth/callback`
- Navigate to TIM and build the image `docker build -t tim .`
- Clone [Chat Widget](https://github.com/buerokratt/Chat-Widget)
- build chat widget image `docker build -f Dockerfile.dev -t chat-widget .`
- Clone [Customer Service](https://github.com/buerokratt/Customer-service)
- build chat widget image `docker build -f Dockerfile.dev -t customer-service .`
- Install Python
- Navigate to setup and run `python3 generate-certs.py`
- Navigate to current repo and run `docker-compose up -d`
- Go to http://localhost:3004/et/dev-auth

### Database setup

- For setting up the database initially, run
  `docker run --platform linux/amd64 --network=bykstack riaee/byk-users-db:liquibase20220615 --url=jdbc:postgresql://users_db:5432/byk --username=byk --password=01234 --changelog-file=./master.yml update`
- Run migrations added in this repository by running the helper script `./migrate.sh`
- When creating new migrations, use the helper `./create-migration.sh name-of-migration` which will create a timestamped file in the correct directory and add the required headers

### TIM Cookies

- To be able to use endpoints, you must have a valid cookie generated by TIM so that TIM can validate it
- for example in test DB To create a valid TIM JWT, call cs-login and pass `{"login": "EE30303039914"}` and then copy the value of jwt response and added it as the value for the cookie named `customJwtCookie`

### PR dependencies

- For PDF generation (Saving chat in Chat-Widget) to work - Datamapper image has to be built using this PR https://github.com/buerokratt/DataMapper/pull/12

- flowtest
