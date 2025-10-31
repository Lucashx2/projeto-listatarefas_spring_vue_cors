# 🧩 Projeto: Lista de Tarefas 06 (Spring Boot + Vue.js)

Esse projeto foi uma atividade da faculdade para criar uma aplicação com backend em Spring Boot e frontend em Vue.js. A tarefa principal era achar e corrigir um erro de CORS que foi colocado de propósito.

## 🚀 Como rodar o projeto

Você vai precisar abrir dois terminais.

### 1. Backend (Spring)

No primeiro terminal, entre na pasta `backend`:

```bash
# 1. Entre na pasta do backend
cd backend

# 2. Rode o servidor
mvn spring-boot:run
```

O backend vai rodar em http://localhost:8088.

### 2. Frontend (Vue)

No segundo terminal, entre na pasta do frontend (app-tarefas):

```bash

# 1. Entre na pasta do frontend
cd frontend/app-tarefas

# 2. Instale o que precisa (só na primeira vez)
npm install

# 3. Rode o servidor de desenvolvimento
npm run dev
```

O site vai estar disponível em http://localhost:5173

### 1. Qual era o erro?

Quando eu rodei o projeto, o site abriu, mas não carregou nenhuma tarefa. Abri o console do navegador (F12) e vi este erro:

Access to XMLHttpRequest at 'http://localhost:8088/api/tarefas' from origin 'http://localhost:5173' has been blocked by CORS policy...

Causa do Erro: Isso é o erro de CORS. O navegador, por segurança, não deixou o frontend (que roda na porta :5173) pedir dados para o backend (que roda na porta :8088), porque as "origens" (as portas) são diferentes.

O erro aconteceu porque o backend (Spring) não estava avisando o navegador que o frontend tinha permissão para fazer isso. Faltou o backend enviar o cabeçalho Access-Control-Allow-Origin na resposta.

(Obs: Antes de chegar nesse erro, eu também tive que arrumar um erro de porta no frontend (que estava chamando 8080 em vez de 8088) e um erro 404 porque o caminho no controller estava duplicado (/api/api/tarefas)).

### 2. Como eu corrigi?
Para arrumar o CORS, eu criei uma classe de configuração global no backend. Assim, a regra vale para a API inteira.

Criei este arquivo:

Arquivo: backend/src/main/java/br/com/tarefas/api/config/WebConfig.java

```bash
Java

package br.com.tarefas.api.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**") // 1. Aplica a regra pra API inteira
            .allowedOrigins("http://localhost:5173") // 2. Libera o nosso frontend
            .allowedMethods("GET", "POST", "PUT", "PATCH", "DELETE", "OPTIONS") // 3. Libera os métodos
            .allowedHeaders("*") 
            .allowCredentials(true);
    }
}
```

### Explicação:

1. addMapping("/**"): Faz a regra valer para todos os endereços da API.

2. allowedOrigins("http://localhost:5173"): Ela manda o backend avisar o navegador que o localhost:5173 tem permissão para fazer chamadas.

3. allowedMethods(...): Libera os métodos HTTP que o frontend vai usar (GET, POST, etc.).
