🐳 Docker Swarm — PedidoRápido

PASSO A PASSO COMPLETO (Windows / PowerShell)

=====================================================

1. Entrar na pasta do projeto

cd X:\FACCAT\padroes-arq-sistemas\pedidorapido

=====================================================

2. Construir a imagem da aplicação Flask

(necessário somente se a imagem não existir)

docker build -t pedidorapido-web ./app

=====================================================

3. Inicializar o Docker Swarm

docker swarm init

=====================================================

4. Verificar o nó Manager

docker node ls

=====================================================

5. Criar a rede overlay

docker network create --driver overlay pedido-net

=====================================================

6. Criar o serviço Redis

docker service create --name redis --network pedido-net redis:7-alpine

=====================================================

7. Criar o serviço Web

docker service create --name web --network pedido-net --replicas 1 pedidorapido-web

=====================================================

8. Criar o serviço Nginx

docker service create --name nginx --network pedido-net --publish 80:80 nginx:alpine

=====================================================

9. Verificar os serviços

docker service ls

=====================================================

10. Escalar a aplicação para 5 réplicas

docker service scale web=5

=====================================================

11. Verificar as réplicas

docker service ps web

=====================================================

12. Abrir no navegador

http://localhost

=====================================================

13. Listar containers

docker ps

=====================================================

14. Simular falha

(substituir pelo ID real do container web)

docker rm -f ID_DO_CONTAINER

=====================================================

15. Verificar Self-Healing

docker service ps web

=====================================================

16. Encerrar os serviços

docker service rm nginx web redis

=====================================================

17. Remover a rede

docker network rm pedido-net

=====================================================

18. Sair do Swarm

docker swarm leave --force

=====================================================

19. Ver imagens Docker

docker images

=====================================================

20. Remover imagens NÃO utilizadas

docker image prune -a

=====================================================

21. Remover TODAS as imagens manualmente

docker rmi IMAGE_ID

=====================================================

22. Remover TODOS os containers parados

docker container prune

_______________________________________________________

🎯 Respostas das Perguntas

Q1)
Sem o Redis, o contador não funcionaria corretamente com múltiplas réplicas. Cada container armazenaria o contador apenas em sua própria memória local, fazendo com que cada réplica apresentasse valores diferentes. O Redis centraliza o estado compartilhado, garantindo consistência entre todas as instâncias da aplicação.

---

Q2)
O componente responsável por esconder a troca de containers é o Nginx, atuando como Load Balancer. Ele distribui as requisições entre as réplicas do serviço web sem que o usuário perceba. Isso é importante porque permite alta disponibilidade, balanceamento de carga e escalabilidade transparente para o usuário final.

---

Q3)
Sim. O Redis com apenas 1 réplica se torna um SPOF (Single Point of Failure). Se ele falhar, a aplicação perde acesso ao estado compartilhado e o contador deixa de funcionar corretamente. Em ambientes de produção seria necessário implementar replicação, failover ou Redis Cluster para evitar esse problema.

---

Q4)
As sessões de login precisariam ser armazenadas em um local compartilhado, como Redis ou banco de dados. Isso ocorre porque diferentes requisições do mesmo usuário podem ser atendidas por containers diferentes, então as informações de sessão não podem ficar apenas na memória local de uma única réplica.

---

Q5)
O Redis não escala da mesma forma que o serviço web porque ele mantém estado compartilhado. Múltiplas réplicas do Redis exigem sincronização de dados, replicação e mecanismos de consistência. Já o serviço web é stateless, ou seja, não guarda estado local permanente, tornando sua escalabilidade muito mais simples.

---

Q6)
Para esse setup funcionar em múltiplas máquinas físicas seria necessário instalar Docker em todos os nós, conectar os servidores ao cluster Swarm usando docker swarm join, garantir comunicação de rede entre as máquinas, configurar a rede overlay e disponibilizar as imagens Docker para todos os nós do cluster.

