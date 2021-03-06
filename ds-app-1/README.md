# Tarefa: criar um aplicativo web multisserviço e distribuído por múltiplos nós 

## Objetivo: criar redes, volumes e serviços para um aplicativo de votação "gatos ou cachorros" baseado na web.

- No arquivo architecture.png, neste diretório, você encontrará um diagrama básico de como os 5 serviços funcionarão
- Todas as imagens Docker usadas estão no Docker Hub. Sugiro que você use seu editor para criar seus comandos localmente e, em seguida, colá-los no shell do Swarm
- Serão necessárias 2 redes overlay: uma de `backend` e uma de `frontend`. As redes são idênticas, mas diferem no papel desempenhado. A rede de backend ajudará a proteger o banco de dados do aplicativo de votação (semelhante a uma configuração VLAN em uma arquitetura tradicional)
- O servidor de banco de dados deve usar um volume nomeado para preservar os dados. Use o formato `--mount` para fazer isto:`--mount type=volume,source=db-dados,target=/var/lib/postgresql/data`

### Serviços (os nomes abaixo devem ser os nomes dos serviços)
- vote
    - imagem Docker: dockersamples/examplevotingapp_vote:before
    - aplicativo web para os usuários votarem em "dog" ou "cat"
    - Idealmente publicado na porta 80/TCP. O container usa a porta 80
    - na rede frontend
    - 2+ réplicas deste container

- redis
    - imagem Docker: redis
    - armazenamento de chave/valor para votos recebidos
    - sem portas públicas
    - na rede frontend
    - 1 réplica

- worker
    - imagem Docker: mfrancoatdocker/examplevotingapp_worker
    - processador backend de redis e que armazena os resultados em postgres
    - sem portas públicas
    - nas redes de frontend e backend
    - 1 réplica

- db
    - imagem Docker: postgres
    - necessita de um volume nomeado, apontando para /var/lib/postgresql/data
    - na rede de backend
    - 1 réplica

- result
    - imagem Docker: dockersamples/examplevotingapp_result:before
    - aplicativo web que mostra os resultados da votação
    - funciona em uma porta alta (imaginando que seja um aplicativo para acesso de administradores)
    - execute em uma porta alta de sua escolha (exemplo: 5001). O container usa a porta 80
    - na rede de backend
    - 1 réplica