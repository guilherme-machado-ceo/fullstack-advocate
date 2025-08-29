# Deploy Configuration

## AWS S3 Deployment Setup

Este projeto está configurado para deploy automático no AWS S3 via GitHub Actions.

### Pré-requisitos

1. **Conta AWS** com permissões para S3 e CloudFront
2. **Bucket S3** configurado para hosting estático
3. **CloudFront Distribution** (opcional, mas recomendado)
4. **GitHub Secrets** configurados

### Configuração do GitHub Secrets

Adicione os seguintes secrets no seu repositório GitHub:

```
AWS_ACCESS_KEY_ID=your_aws_access_key
AWS_SECRET_ACCESS_KEY=your_aws_secret_key
S3_BUCKET_NAME=your-bucket-name
CLOUDFRONT_DISTRIBUTION_ID=your_cloudfront_id (opcional)
```

### Configuração do Bucket S3

1. **Criar bucket S3:**
   ```bash
   aws s3 mb s3://your-bucket-name --region us-east-1
   ```

2. **Configurar para website estático:**
   ```bash
   aws s3 website s3://your-bucket-name --index-document index.html --error-document error.html
   ```

3. **Configurar política do bucket:**
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "PublicReadGetObject",
         "Effect": "Allow",
         "Principal": "*",
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::your-bucket-name/*"
       }
     ]
   }
   ```

### Configuração do CloudFront (Recomendado)

1. **Criar distribuição CloudFront**
2. **Configurar origem para o bucket S3**
3. **Configurar comportamentos de cache**
4. **Adicionar certificado SSL/TLS**

### Workflow de Deploy

O deploy é executado automaticamente quando:
- Push para branch `main` ou `master`
- Pull request para branch `main` ou `master` (apenas testes)

### Estrutura do Pipeline

1. **Test Job:**
   - Instala dependências
   - Executa linting
   - Executa testes
   - Gera build

2. **Deploy Job:**
   - Executa apenas em push para main/master
   - Faz upload para S3
   - Invalida cache do CloudFront

3. **Security Scan Job:**
   - Executa apenas em pull requests
   - Verifica vulnerabilidades
   - Executa auditoria de segurança

### Comandos Úteis

```bash
# Deploy manual local
npm run build
aws s3 sync dist/ s3://your-bucket-name --delete

# Invalidar cache CloudFront
aws cloudfront create-invalidation --distribution-id YOUR_ID --paths "/*"

# Verificar status do bucket
aws s3 ls s3://your-bucket-name
```

### Monitoramento

- **CloudWatch:** Monitore métricas de S3 e CloudFront
- **GitHub Actions:** Acompanhe logs de deploy
- **AWS Cost Explorer:** Monitore custos

### Troubleshooting

**Erro de permissões:**
- Verifique se as credenciais AWS estão corretas
- Confirme se o usuário tem permissões para S3

**Erro de build:**
- Verifique se todas as dependências estão instaladas
- Confirme se o comando de build está correto

**Erro de CloudFront:**
- Verifique se o Distribution ID está correto
- Confirme se o usuário tem permissões para CloudFront

### Segurança

- Use IAM roles com permissões mínimas necessárias
- Rotacione credenciais regularmente
- Monitore logs de acesso
- Configure alertas para atividades suspeitas

### Performance

- Configure cache headers apropriados
- Use CloudFront para CDN global
- Otimize imagens e assets
- Implemente compressão gzip