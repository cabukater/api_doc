# API - APP SuperAgendador Clientes

#####  **A - AUTENTICAÇÃO (modelo WhatsApp)**
###### Fluxo:
**1**. A primeira tela de autenticação, é um formulário onde o cliente informa apenas o seu telefone celular DDD + NUMERO, ao enviar o sistema irá gerar um código que será encaminhado por SMS para o mesmo número garantindo que o celular informado é dele mesmo;
** 1.1 **Caso telefone não seja encontrado, tem que direcionar para uma tela de cadastro pedindo NOME e E-MAIL (API pendente), telefone já tem do post da primeira tela;

**2.** Após cliente identificado/cadastrado, na segunda tela ele informa o código recebido por SMS;

**3.** Depois de fazer o passo 1 e 2 o telefone e o HASH de senha serão devolvidos para que nos próximos acessos o cliente seja autenticado automaticamente.

###### POST:
    https://sac.superagendador.com/api/1.1/buscaloginclient.php
###### HEADERS:
```javascript
Authentication: Basic HASH_ADMIN_API
Content-Type: application/x-www-form-urlencoded
```
##### Passo 1 - Solicita Código SMS de validação do usuário:

###### PARAMS:

    telefone  (telefone com DDD somente números, sempre salvar numa localStorage)
   
###### RETURN:


    {
      codigo_sms: true    (código SMS enviado com sucesso!)
      message: "OK"
      status: true
    }
     
    

------------



##### Passo 2 - Autentica passando o código recebido por SMS:
###### PARAMS:
```javascript
telefone (telefone com DDD somente números)
codigo_sms	(apenas no segundo passo, apos o cliente receber o Código via SMS)
deviceid	(deviceid capturado pelo APP)

```
###### RETURN:
```javascript
{
  status:true
  data:[{domain: "beta2.superagendador.com", base: "aflordap", id_user: "203",…},…],
  message:"OK",
  password:"HASH_AUTH_CLIENTE"   (após autenticação via Código SMS gravar numa localStorage o hash de autenticação)
}

```
------------
##### Passo 3 - Verifica se o cliente já está logado e se password ainda é válido:
###### PARAMS:
```javascript
telefone: 11999999999
password: HASH_AUTH_CLIENTE
 
```
###### RETURN:

    {
      status:true,
      data: [{domain: "rafateste.superagendador.com", base: "rafatest", id_user: "2", nome_empresa: "Rafa Teste"}],
      message:"OK"}
    


------------

#### B - LISTA DE AGENDAMENTOS
###### Fluxo:

**1.** A primeira tela ao se logar é a de listagem dos agendamentos futuros/marcações do cliente; 
**1.1. **Cada item da lista deve ser clicável para que o cliente indique se Confirma ou Cancela o agendamento;
**1.2. **Um botão alinhado a direita para edição/reagendamento que carrega o formulário de agendamento para troca de horário.
**2.** Em destaque nesta tela deve existir um botão para o menu e outro para fazer um novo Agendamento. Pode usar o app atual como referência.

###### POST:
```javascript
https://sac.superagendador.com/api/1.1/buscaagendamento.php
```
###### HEADERS:


    Authentication: Basic HASH_ADMIN_API
    Content-Type: application/x-www-form-urlencoded
    

##### Passo 1 - Lista agendamentos futuros do cliente:

###### PARAMS:


    username	(telefone com DDD somente números, sempre salvar numa localStorage)
    password	(hash de autenticação salvo na localStorage após validação via SMS)
    history		(Opcional history=1 carrega histórico de agendamentos do cliente, não permitir nenhuma iteração nessa lista)
    
###### RETURN:


    [{
    	"id": "907",
    	"title": "SuperAgendador.com",
    	"atendente": "Grazi",
    	"start": "2016-10-07T14:00:00Z",
    	"end": "2016-10-07T15:00:00Z",
    	"servico": "Entrevista",
    	"resp": "12",
    	"status_agendamento": "2",
    	"comanda": null,
    	"comentario": "",
    	"base": "julianot"
    }]
    

------------
### C - EDITAR AGENDAMENTO

###### Fluxo:
**1.** Na primeira tela ao clicar em um agendamento, abrir os dados do agendamento com possibilidade de edição.

###### POST:

    https://sac.superagendador.com/api/1.1/gatewayclient.php

###### HEADERS:


    Authorization: Basic HASH_ADMIN_API
    Content-Type: application/x-www-form-urlencoded
    
##### Passo 1 - Lista dados do agendamento selecionado:
###### PARAMS:


    option: com_consultas
    task: listjson
    username: 11999118225
    password: d2508279b202354dc2aa857f7e1f5b9b
    id: 907
    base: julianot
    

###### RETURN:


    {
    	"id": "907",
    	"id_servico": "20",
    	"lembrete": "1",
    	"status": "2",
    	"id_responsavel": "12",
    	"data": "2016-10-07",
    	"horario": "14:00:00",
    	"hora_fim": "15:00:00",
    	"id_paciente": "9",
    	"nome_responsavel": "Grazi",
    	"descricao_servico": "Entrevista",
    	"id_comanda": "0"
    }
    
------------

##### Passo 2 - Salvar dados do agendamento: 
(Para salvar um agendamento Novo basta passar o parametro **id = 0**)
  
######   PARAMS:


    option: com_consultas
    task: savemobile
    username: (11) 9991-18225
    password: d2508279b202354dc2aa857f7e1f5b9b
    id_servico: 20
    id_responsavel: 12
    hora_inicio: 18:00
    data: 07/10/2016
    id_paciente: 9
    id: 907
    base: julianot
    
  
   
    
######   RETURN:  

     {
        	"status":true,
        	"message":"ok"
        }
------------

#### Confirmar ou Cancelar Agendamento

###### Fluxo:

**1.** Na primeira tela ao clicar em um agendamento, abrir modal com opção de Confirmar ou Cancelar Agendamento.
**2. **Ao clicar em uma das opções invocar a seguinte API:
###### POST:

    https://sac.superagendador.com/api/1.1/gatewayclient.php

###### HEADERS:


    Authorization: Basic HASH_ADMIN_API
    Content-Type: application/x-www-form-urlencoded
    

###### PARAMS:


    option: com_consultas
    task: updatestatus
    username: (11) 9991-18225
    password: d2508279b202354dc2aa857f7e1f5b9b
    id: 907
    status: 2     (2 = Confirmar e 7 = Cancelar)
    base: julianot
    

###### RETURN:


    {
     status: true,
     message: "ok"
    }
    
