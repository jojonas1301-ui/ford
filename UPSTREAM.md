# Origem do backend

Este projeto incorpora uma cópia do backend oficial Nuvio Self-Host:

- Repositório: https://github.com/NuvioMedia/self-host
- Commit importado: `30f156807ae77b2aec80b091b0d513841a25ab91`
- Licença: Apache License 2.0; consulte `LICENSE` e `NOTICE`.

Os arquivos de execução do backend, Compose, migrações, scripts e testes foram
mantidos como no commit de origem. O README principal foi substituído por uma
orientação em português e foi acrescentado `docs/guia-duas-tvs.md`.

O workflow interno do projeto original para publicar a imagem do dashboard a
partir de um repositório privado não foi copiado. O backend continua usando a
imagem pública oficial já definida em `compose.yml`. O workflow de validação foi
mantido.

Para consultar correções posteriores do projeto oficial:

```sh
git ls-remote https://github.com/NuvioMedia/self-host.git refs/heads/main
```

Antes de atualizar uma instalação em produção, revise as mudanças oficiais e
execute `./nuvio backup`.
