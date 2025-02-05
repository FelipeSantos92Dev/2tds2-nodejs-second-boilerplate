# Projeto NodeJS Boilerplate

## Descrição

Projeto base em NodeJS

## Passo a Passo para Desenvolvimento

### 1. Inicialização do Projeto

1. Inicialize um novo projeto Node.js:

   ```sh
   npm init -y
   ```

2. Instale as dependências necessárias:
   ```sh
   npm install express dotenv nodemon
   ```

### 2. Estrutura de Pastas

Crie a estrutura de pastas do projeto

### 3. Configuração do Servidor

1. Crie o arquivo [.env](http://_vscodecontentref_/1) para definir a porta do servidor:

   ```properties
   PORT=4000
   ```

2. Crie o arquivo [server.js](http://_vscodecontentref_/2) para configurar o servidor Express:

   ```js
   import express from "express";
   import { config } from "dotenv";

   import routes from "./routes/index.routes.js";

   config();

   const serverPort = process.env.PORT || 3000;

   const app = express();
   app.use(express.json());
   app.use(routes);

   app.listen(serverPort, () => {
     console.log(`⚡ Server started on http://localhost:${serverPort}`);
   });
   ```

### 4. Configuração das Rotas

1. Crie o arquivo [index.routes.js](http://_vscodecontentref_/3) para definir as rotas principais:

   ```js
   import { Router } from "express";

   // Lista de importação das rotas do projeto
   import usuariosRoutes from "./usuarios.routes.js";

   const routes = Router();

   // Rota raiz para teste
   routes.get("/", (req, res) => {
     return res.status(200).json({ message: "Vai Corinthians!" });
   });

   // Lista de uso das rotas do projeto
   routes.use("/usuarios", usuariosRoutes);

   export default routes;
   ```

2. Crie o arquivo [usuarios.routes.js](http://_vscodecontentref_/4) para definir as rotas de usuários:

   ```js
   import { Router } from "express";
   import UsersRepository from "../models/users/UsersRepository.js";

   const usuariosRoutes = Router();

   // Criação da instância do repositório de usuários
   const usersRepository = new UsersRepository();

   usuariosRoutes.get("/", (req, res) => {
     const usuarios = usersRepository.getAllUsers();

     return res.status(200).json({
       message:
         usuarios.length == 0
           ? "Não há usuários cadastrados"
           : `Total de usuários: ${usuarios.length}`,
       usuarios,
     });
   });

   usuariosRoutes.post("/", (req, res) => {
     const { name, email, password } = req.body;

     const usuario = usersRepository.addUser(name, email, password);

     return res.status(201).json({
       message: "Usuário cadastrado com sucesso!",
       usuario,
     });
   });

   usuariosRoutes.get("/:id", (req, res) => {
     const { id } = req.params;
     const user = usersRepository.getUserById(id);

     if (!user) {
       return res.status(404).json({
         message: `Usuário com id ${id} não encontrado!`,
       });
     }

     return res.status(200).json({
       message: `Usuário com id ${id} encontrado!`,
       user,
     });
   });

   usuariosRoutes.put("/:id", (req, res) => {
     const { id } = req.params;
     const { name, email, password } = req.body;

     const user = usersRepository.updateUser(id, name, email, password);

     if (!user) {
       return res.status(404).json({
         message: `Usuário com id ${id} não encontrado!`,
       });
     }

     return res.status(200).json({
       message: `Usuário com id ${id} atualizado com sucesso!`,
       user,
     });
   });

   usuariosRoutes.delete("/:id", (req, res) => {
     const { id } = req.params;

     const user = usersRepository.deleteUser(id);

     if (!user) {
       return res.status(404).json({
         message: `Usuário com id ${id} não encontrado!`,
       });
     }

     return res.status(200).json({
       message: `Usuário com id ${id} deletado com sucesso!`,
       user,
     });
   });

   export default usuariosRoutes;
   ```

### 5. Modelos de Usuários

1. Crie o arquivo [User.js](http://_vscodecontentref_/5) para definir o modelo de usuário:

   ```js
   class User {
     constructor(name, email, password) {
       this.id = this.generateId();
       this.name = name;
       this.email = email;
       this.password = password;
     }

     generateId() {
       return Math.floor(Math.random() * 999) + 1;
     }
   }

   export default User;
   ```

2. Crie o arquivo [UsersRepository.js](http://_vscodecontentref_/6) para definir o repositório de usuários:

   ```js
   import User from "./User.js";

   class UsersRepository {
     constructor() {
       this.users = [];
     }

     getAllUsers() {
       return this.users;
     }

     addUser(name, email, password) {
       const newUser = new User(name, email, password);

       this.users.push(newUser);

       return newUser;
     }

     getUserById(id) {
       const user = this.users.find((u) => u.id == id);

       return user;
     }

     updateUser(id, name, email, password) {
       const user = this.getUserById(id);

       if (!user) {
         return null;
       }

       user.name = name;
       user.email = email;
       user.password = password;

       return user;
     }

     deleteUser(id) {
       const user = this.getUserById(id);

       if (!user) {
         return null;
       }

       this.users = this.users.filter((u) => u.id != id);

       return user;
     }
   }

   export default UsersRepository;
   ```

### 6. Execução do Projeto

1. Adicione o script de desenvolvimento no [package.json](http://_vscodecontentref_/7):

   ```json
   "scripts": {
     "dev": "nodemon src/server.js"
   }
   ```

2. Execute o projeto:
   ```sh
   npm run dev
   ```

Pronto! Agora você tem um projeto Node.js configurado com rotas e modelos de usuários.
