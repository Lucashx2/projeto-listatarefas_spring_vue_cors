🧩 Projeto: Lista de Tarefas 06 (Spring Boot + Vue.js)

Este projeto foi desenvolvido como uma atividade da faculdade, com o objetivo de criar uma aplicação fullstack usando Spring Boot no backend e Vue.js no frontend.
A principal tarefa era encontrar e corrigir um erro de CORS inserido propositalmente no código.

🚀 Como Rodar o Projeto

Você precisará abrir dois terminais — um para o backend e outro para o frontend.

🖥️ 1. Backend (Spring Boot)

No primeiro terminal:

# 1. Entre na pasta do backend
cd backend

# 2. Rode o servidor
mvn spring-boot:run


O backend estará disponível em:
👉 http://localhost:8088

🌐 2. Frontend (Vue.js)

No segundo terminal:

# 1. Entre na pasta do frontend
cd frontend/app-tarefas

# 2. Instale as dependências (apenas na primeira vez)
npm install

# 3. Rode o servidor de desenvolvimento
npm run dev


O site estará disponível em:
👉 http://localhost:5173

❌ Qual Era o Erro?

Ao rodar o projeto, o site abria, mas não carregava nenhuma tarefa.
No console do navegador (F12), aparecia o seguinte erro:

Access to XMLHttpRequest at 'http://localhost:8088/api/tarefas' from origin 'http://localhost:5173' has been blocked by CORS policy...

🧠 Causa do Erro

O erro era de CORS (Cross-Origin Resource Sharing).
O navegador bloqueou o frontend (porta 5173) de acessar o backend (porta 8088), pois as origens são diferentes.

O problema ocorreu porque o backend não estava enviando o cabeçalho:

Access-Control-Allow-Origin


sem o qual o navegador não autoriza a comunicação entre origens distintas.

💡 Antes disso, também foi necessário corrigir:

A porta errada no frontend (estava chamando 8080 ao invés de 8088).

Um erro 404, causado por duplicação no caminho do controller (/api/api/tarefas).

🛠️ Como Corrigi o Problema

A solução foi criar uma configuração global de CORS no backend, válida para toda a API.

📄 Arquivo:
backend/src/main/java/br/com/tarefas/api/config/WebConfig.java

package br.com.tarefas.api.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**") // 1. Aplica a regra para toda a API
            .allowedOrigins("http://localhost:5173") // 2. Libera o frontend
            .allowedMethods("GET", "POST", "PUT", "PATCH", "DELETE", "OPTIONS") // 3. Métodos permitidos
            .allowedHeaders("*")
            .allowCredentials(true);
    }
}

🔍 Explicação

addMapping("/**") → Aplica a configuração a todos os endpoints da API.

allowedOrigins("http://localhost:5173") → Permite que o frontend local acesse o backend.

allowedMethods(...) → Libera os métodos HTTP necessários (GET, POST, PUT, etc.).

allowedHeaders("*") → Permite qualquer cabeçalho enviado pelo cliente.

allowCredentials(true) → Autoriza o envio de cookies ou autenticações se necessário.

✅ Resultado Final

Após a correção, o frontend conseguiu se comunicar normalmente com o backend, carregando as tarefas sem erros de CORS.
O projeto passou a funcionar como esperado. 🎯

🧑‍💻 Tecnologias Utilizadas

Backend: Spring Boot

Frontend: Vue.js (Vite)

Gerenciador de Dependências: Maven / npm

Portas padrão:

Backend → http://localhost:8088

Frontend → http://localhost:5173

📚 Autor

Desenvolvido como parte das atividades do curso de Análise e Desenvolvimento de Sistemas.
