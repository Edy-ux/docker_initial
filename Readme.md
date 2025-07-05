# Etapa de build

#### Usa a imagem com o SDK do .NET 8 (capaz de compilar e publicar o projeto)

```DockerFile
  FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
```

### Define o diretório de trabalho dentro do container como /app
```DockerFile
  WORKDIR /app
```

### Copia todos os arquivos do projeto local para dentro do diretório /app no container

```DockerFile
 COPY . ./ <app>
```
### Restaura as dependências do projeto (executa o restore do NuGet)

```dockerFile
RUN dotnet restore
```

# Compila o projeto em modo Release e gera os arquivos finais na pasta 'out'
```dockerFile
RUN dotnet publish -c Release -o out
```

# Etapa final: imagem mais enxuta com runtime
Usa apenas o runtime do ASP.NET Core (não tem SDK, apenas roda aplicações já publicadas)

```dockerFile
FROM mcr.microsoft.com/dotnet/aspnet:8.0
```



# Define o diretório de trabalho como /app novamente

```dockerFile
WORKDIR /app
```

# Copia os arquivos publicados da etapa "build" para a imagem final

🔹 --from=build <br>

 Refere-se ao apelido da etapa anterior do Dockerfile:

 AS build dá o nome build à etapa de compilação. Assim, nesta etapa final, você consegue "enxergar" o que foi gerado lá dentro.
```sh
 FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
```
 🔹 /app/out <br>

 É o caminho dentro da imagem build onde estão os arquivos publicados pela aplicação (o resultado do dotnet publish -o out).

 🔹 .
Esse . significa: copie para o diretório atual do container destino, que no caso é:
```dockerFile
COPY --from=build /app/out .
```
### Comando abaixo Expõe a porta 80 (usada para acessar a aplicação via HTTP)

```docker
EXPOSE 80
```
### Define o comando padrão quando o container inicia: executar a DLL da aplicação usando o dotnet

```docker
ENTRYPOINT ["dotnet", "MinhaApiDocker.dll"]
```
✅ **Nomeie e versione sua imagem**

```bash
 > docker build -t minha-api-docker:v1 .
```

### Instanciar imagem/Docker 


```bash
docker run -d -p 8080:80 --name  API<CONTAINER> asp.net-webapi<IMAGE>
```



