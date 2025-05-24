
# NextPark API

Projeto desenvolvido para a Sprint 1 da disciplina **DevOps Tools & Cloud Computing** do curso de Análise e Desenvolvimento de Sistemas da FIAP.

## 📝 Descrição do Projeto

O NextPark é uma API REST desenvolvida em .NET 8 que simula a gestão de estacionamento inteligente para motos. O sistema identifica motos que entram no pátio e realiza a alocação automática em vagas disponíveis. A API foi conteinerizada com Docker e publicada no Docker Hub para ser executada remotamente em uma VM na Azure.

## 📌 Rotas da API

- `GET /api/moto` – Lista todas as motos cadastradas
- `GET /api/moto/{id}` – Retorna uma moto específica
- `POST /api/moto` – Cadastra uma nova moto
- `PUT /api/moto/{id}` – Atualiza os dados de uma moto
- `DELETE /api/moto/{id}` – Remove uma moto

*Todas as rotas podem ser testadas via Swagger UI após iniciar a aplicação.*

## 🛠️ Instalação

### Executar localmente com Docker:

```bash
docker build -t tcapela/nextpark-api .
docker run -d -p 80:8080 -e ASPNETCORE_ENVIRONMENT=Development tcapela/nextpark-api
```

Acesse no navegador: http://localhost/swagger

### Sem Docker (direto via .NET CLI):

```bash
dotnet build
dotnet run
```

## 🐳 Dockerfile

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY . .
RUN dotnet publish "NextParkAPI.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=build /app/publish .
ENTRYPOINT ["dotnet", "NextParkAPI.dll"]
```

## ☁️ Scripts do Azure CLI

```bash
az group create --name nextpark-grupo --location brazilsouth
az vm create --resource-group nextpark-grupo --name vm-nextpark --image Ubuntu2204 --admin-username azureuser --generate-ssh-keys
az vm open-port --resource-group nextpark-grupo --name vm-nextpark --port 80
ssh azureuser@<IP_DA_VM>
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo docker run -d -p 80:8080 -e ASPNETCORE_ENVIRONMENT=Development tcapela/nextpark-api
az group delete --name nextpark-grupo --yes --no-wait
```

---

## 👤 Desenvolvedores

- Tiago Ribeiro Capela – RM 558021  
- Raphaela Oliveira Tatto – RM 554983
