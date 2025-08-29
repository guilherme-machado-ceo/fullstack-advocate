# Configuração do Bucket S3 para Site Estático

## 1. Criar o Bucket S3

1. Acesse o AWS Console > S3
2. Clique em "Create bucket"
3. Escolha um nome único para o bucket (ex: `meu-site-fullstack-advocate`)
4. Região: `us-east-1` (ou a região de sua preferência)
5. **IMPORTANTE**: Desmarque "Block all public access"
6. Marque a confirmação de que o bucket será público
7. Clique em "Create bucket"

## 2. Configurar Static Website Hosting

1. Entre no bucket criado
2. Vá para a aba "Properties"
3. Role até "Static website hosting"
4. Clique em "Edit"
5. Selecione "Enable"
6. Index document: `index.html`
7. Error document: `index.html` (para SPAs)
8. Clique em "Save changes"

## 3. Aplicar a Política do Bucket

1. Vá para a aba "Permissions"
2. Role até "Bucket policy"
3. Clique em "Edit"
4. Cole o conteúdo do arquivo `s3-bucket-policy.json`
5. **SUBSTITUA** `SEU-BUCKET-NAME` pelo nome real do seu bucket
6. Clique em "Save changes"

## 4. Configurar CORS (se necessário)

```json
[
    {
        "AllowedHeaders": ["*"],
        "AllowedMethods": ["GET", "HEAD"],
        "AllowedOrigins": ["*"],
        "ExposeHeaders": []
    }
]
```

## 5. URL do Site

Após a configuração, seu site estará disponível em:
`https://SEU-BUCKET-NAME.s3-website-us-east-1.amazonaws.com`

## 6. Secrets do GitHub

Configure estes secrets no seu repositório GitHub:
- `AWS_ACCESS_KEY_ID`: Sua chave de acesso AWS
- `AWS_SECRET_ACCESS_KEY`: Sua chave secreta AWS
- `S3_BUCKET_NAME`: Nome do bucket criado
- `CLOUDFRONT_DISTRIBUTION_ID`: (Opcional) Para CDN

## 7. Permissões IAM Necessárias

Sua chave AWS precisa das seguintes permissões:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:GetObject",
                "s3:DeleteObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::SEU-BUCKET-NAME",
                "arn:aws:s3:::SEU-BUCKET-NAME/*"
            ]
        }
    ]
}
```

## 8. CloudFront (Opcional - CDN)

Para melhor performance:
1. Crie uma distribuição CloudFront
2. Origin: Endpoint do S3 website
3. Configure cache behaviors
4. Adicione o Distribution ID nos secrets do GitHub