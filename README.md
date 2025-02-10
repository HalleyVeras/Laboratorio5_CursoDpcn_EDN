<div align="center" style="font-family: Arial, sans-serif; font-size: 20px; line-height: 1.5;">

# 🌟 **Curso Dpcn-EDN** 🌟
# 🌟 Solutions Architect Associate  🌟

###
<a href="https://escoladanuvem.org"><a href="https://aws.amazon.com/pt/?nc2=h_lg">
    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/93/Amazon_Web_Services_Logo.svg/2560px-Amazon_Web_Services_Logo.svg.png" width="180" alt="AWS Logo">
</a>
<img src="https://cdn.worldvectorlogo.com/logos/amazon-s3-simple-storage-service.svg" width="80" alt="AWS Logo">- <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRRsHozuh-tDR3wCxV5bR-TI04bRbnVmfISpA&s" width="80" alt="AWS Logo">-<img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT2t15BW3x6w9Mnp-ZJbJa2hhzsE4ABFdDrig&s" width="80" alt="AWS Logo">- 
    <img src="https://github.com/HalleyVeras/Escola_da_Nuvem/blob/main/Documentos/download%20(4)_processed.png?raw=true" width="210" alt="Second Image">
</a>
</div>

# Laboratório 5 : Integração e Mensageria com SQS e SNS no Amazon S3

**Turma:** Dpcn 07  
**Aluno:** Halley Veras  
## Objetivo
Este guia explica como configurar notificações por e-mail (**via SNS**) e registro de eventos em fila (**via SQS**) para uploads e exclusões de arquivos em um bucket S3.

## Cenário
Trabalhamos em uma empresa de mídia que armazena arquivos de imagem e vídeo no **Amazon S3**. O objetivo é:
- Receber **notificações por e-mail** sempre que um arquivo for enviado ou excluído do bucket.
- Registrar todos os eventos de upload e exclusão em uma **fila SQS** para auditoria e monitoramento.

## Pré-requisitos
- Conta AWS ativa
- Permissões para S3, SNS e SQS
- Endereço de e-mail válido

## **Passo 1: Criar o Bucket S3**
1. Acesse o console do **Amazon S3**.
2. Clique em **Criar bucket**.
3. Defina o nome do bucket: `eventos-s3-seunome-data`.
4. Escolha a região **us-east-1**.
5. Deixe as demais configurações padrão.
6. Clique em **Criar bucket**.
7. **Copie o ARN do bucket** (será usado depois).

<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos/painel_aws.jpg" width="400" alt="AWS Logo">-
<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos/Painel_s3.jpg" width="400" alt="AWS Logo">-
<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos/bucket_criado.jpg" width="400" alt="AWS Logo">-
<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos/arn_copiado.jpg" width="400" alt="AWS Logo">-


---

## **Passo 2: Criar um Tópico SNS**
1. Acesse o console do **Amazon SNS**.
2. Clique em **Tópicos** > **Criar tópico**.
3. Escolha o tipo **Padrão**.
4. Defina um nome: `notificacoes-s3-seunome-data`.
5. Clique em **Criar tópico**.
6. **Copie o ARN do tópico**.

<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos/painel_sns.jpg" width="400" alt="AWS Logo">-
<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos/topic_criado_1.jpg" width="400" alt="AWS Logo">-
<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos/topic_criado.jpg" width="400" alt="AWS Logo">-


---

## **Passo 3: Criar Assinatura de E-mail no SNS**
1. No tópico criado, clique em **Assinaturas** > **Criar assinatura**.
2. Escolha **Protocolo: E-mail**.
3. Digite seu e-mail.
4. Clique em **Criar assinatura**.
5. Acesse seu e-mail e **confirme a assinatura**.


<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos/criando_subscription.jpg" width="400" alt="AWS Logo">-
<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos/criando_subscription_final.jpg" width="400" alt="AWS Logo">-
<img src="https://github.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/blob/main/arquivos/confirmacao_email.jpg?raw=true" width="400" alt="AWS Logo">-
<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos/subscripition_confirmacao.jpg" width="250" alt="AWS Logo">-


---

## **Passo 4: Criar uma Fila SQS**
1. Acesse o console do **Amazon SQS**.
2. Clique em **Criar fila**.
3. Escolha o tipo **Padrão**.
4. Defina o nome: `fila-eventos-s3-seunome-data`.
5. Clique em **Criar fila**.
6. **Copie o ARN da fila**.

<img src="https://github.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/blob/main/arquivos2/painel_sqs.jpg?raw=true" width="400" alt="AWS Logo">-
<img src="https://github.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/blob/main/arquivos2/criando_fila.jpg?raw=true" width="400" alt="AWS Logo">-
<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos2/fila_criada.jpg" width="400" alt="AWS Logo">-


---

## **Passo 5: Configurar Permissões no SNS e SQS**
### **Política do SNS (S3 -> SNS)**
1. No console SNS, acesse o tópico criado.
2. Clique em **Política de acesso** > **Editar**.
3. Substitua a política pelo seguinte JSON, preenchendo os ARNs:

```json
{
  "Version": "2008-10-17",
  "Id": "__default_policy_ID",
  "Statement": [
    {
      "Sid": "__default_statement_ID",
      "Effect": "Allow",
      "Principal": { "AWS": "*" },
      "Action": [
        "SNS:Publish",
        "SNS:RemovePermission",
        "SNS:SetTopicAttributes",
        "SNS:DeleteTopic",
        "SNS:ListSubscriptionsByTopic",
        "SNS:GetTopicAttributes",
        "SNS:Receive",
        "SNS:AddPermission",
        "SNS:Subscribe"
      ],
      "Resource": "arn:aws:sns:us-east-1:160885266406:notificacoes-s3-halley-veras-09022025",
      "Condition": { "StringEquals": { "AWS:SourceOwner": "160885266406" } }
    },
    {
      "Sid": "AllowS3ToPublishToTopic",
      "Effect": "Allow",
      "Principal": { "Service": "s3.amazonaws.com" },
      "Action": "SNS:Publish",
      "Resource": "arn:aws:sns:us-east-1:160885266406:notificacoes-s3-halley-veras-09022025",
      "Condition": { "ArnLike": { "aws:SourceArn": "arn:aws:s3:::eventos-s3-halley-veras-09022025" } }
    }
  ]
}
```
<img src="https://github.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/blob/main/arquivos2/politicas_sns_alterada.jpg?raw=true" width="400" alt="AWS Logo">-

4. Clique em **Salvar alterações**.

## **Passo 6: Configurar Política da Fila SQS (SNS -> SQS)**
1. No console SQS, acesse a fila criada.
2. Clique em **Política de acesso** > **Editar**.
3. Substitua a política pelo seguinte JSON:

```json
{
    "Version": "2012-10-17",
    "Id": "__default_policy_ID",
    "Statement": [
        {
            "Sid": "__default_statement_ID",
            "Effect": "Allow",
            "Principal": { "AWS": "*" },
            "Action": [
                "SQS:SendMessage",
                "SQS:ReceiveMessage",
                "SQS:DeleteMessage",
                "SQS:GetQueueAttributes",
                "SQS:SetQueueAttributes",
                "SQS:ListQueues"
            ],
            "Resource": "arn:aws:sqs:us-east-1:160885266406:fila-eventos-s3-halley-veras-09022025"
        },
        {
            "Sid": "AllowSNSToSendMessageToSQS",
            "Effect": "Allow",
            "Principal": { "Service": "sns.amazonaws.com" },
            "Action": "sqs:SendMessage",
            "Resource": "arn:aws:sqs:us-east-1:160885266406:fila-eventos-s3-halley-veras-09022025",
            "Condition": {
                "ArnEquals": {
                    "aws:SourceArn": "arn:aws:sns:us-east-1:160885266406:notificacoes-s3-halley-veras-09022025"
                }
            }
        }
    ]
}
```
<img src="https://github.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/blob/main/arquivos2/alterando_politica_sqs_feita.jpg?raw=true" width="400" alt="AWS Logo">-

## **Passo 7: Configurar Notificações no S3**
1. No console S3, acesse o bucket criado.
2. Vá para **Propriedades** > **Notificações de eventos** > **Criar notificação de evento**.
3. **Configuração:**
   - Nome: `NotificarPutDelete`.
   - Tipos de eventos: 
     - `s3:ObjectCreated:*` (Uploads)
     - `s3:ObjectRemoved:*` (Exclusões)
   - Destino: **Tópico do SNS** > Selecione o seu tópico.
4. Clique em **Salvar**.


<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos3/painel_s3.jpg" width="400" alt="AWS Logo">-
<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos3/propriedades_s3.jpg" width="400" alt="AWS Logo">-
<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos3/create_event_notification_1.jpg" width="400" alt="AWS Logo">-
<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos3/propriedades_s3_02.jpg" width="400" alt="AWS Logo">-
<img src="https://github.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/blob/main/arquivos3/propriedades_s3_03.jpg?raw=true" width="400" alt="AWS Logo">-
<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos3/propriedades_s3_04_sucess.jpg" width="400" alt="AWS Logo">-
<img src="https://raw.githubusercontent.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/refs/heads/main/arquivos3/create_subscription_sns.jpg" width="400" alt="AWS Logo">-

## **troubleshooting:
-  Upload: No S3, carregue um arquivo.
-  Email: Verifique o e-mail.
-  Fila SQS: Pesquise mensagens na fila (deve ter o JSON).
-  Exclusão: No S3, exclua o arquivo.
-  Email e Fila: Verifique novamente.

<img src="https://github.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/blob/main/arquivos4/upload_object.png?raw=true" width="400" alt="AWS Logo">-
<img src="https://github.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/blob/main/arquivos4/email_sqs_upload.png?raw=true" width="400" alt="AWS Logo">-
<img src="https://github.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/blob/main/arquivos4/fila_json_sqs.png?raw=true" width="400" alt="AWS Logo">-
<img src="https://github.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/blob/main/arquivos4/delete_object.png?raw=true" width="400" alt="AWS Logo">-
<img src="https://github.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/blob/main/arquivos4/email_sqs_upload.png?raw=true" width="400" alt="AWS Logo">-
<img src="https://github.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/blob/main/arquivos4/email_sqs_deleteobject.png?raw=true" width="400" alt="AWS Logo">-
<img src="https://github.com/HalleyVeras/Laboratorio5_CursoDpcn_EDN/blob/main/arquivos4/fila_json_sqs_deleteobject.png?raw=true" width="400" alt="AWS Logo">-
