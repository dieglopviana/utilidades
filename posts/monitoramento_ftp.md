# Tutorial: Monitoramento de Transferências FTP (vsftpd)

## 1. Verificar conexões ativas

```bash
# Quantidade de conexões estabelecidas na porta 21
sudo netstat -an | grep :21 | grep ESTABLISHED | wc -l

# Detalhes de cada conexão ativa
sudo lsof -i :21 | grep ESTABLISHED
```

---

## 2. Acompanhar uploads em tempo real

```bash
# Ver os últimos uploads concluídos
grep "OK UPLOAD" /var/log/vsftpd.log | tail -10

# Monitorar ao vivo conforme acontecem
sudo tail -f /var/log/vsftpd.log

# Apenas conexões bem-sucedidas (sem "Connection refused")
grep -v "Connection refused" /var/log/vsftpd.log | tail -20
```

---

## 3. Contar progresso dos uploads do dia

```bash
# Total de uploads concluídos hoje
grep "OK UPLOAD" /var/log/vsftpd.log | grep "$(date '+%b %e')" | wc -l

# Quantos IPs (lojas) únicos já enviaram hoje
grep "OK UPLOAD" /var/log/vsftpd.log | grep "$(date '+%b %e')" | grep -oP '::ffff:\K[\d.]+' | sort -u | wc -l

# Ritmo de uploads por minuto (últimos 10 minutos)
grep "OK UPLOAD" /var/log/vsftpd.log | grep "$(date '+%b %e')" | awk '{print $4}' | cut -d: -f1,2 | sort | uniq -c | tail -10
```

---

## 4. Verificar conexões recusadas

```bash
# Ver conexões recusadas por limite atingido
grep "too many sessions" /var/log/vsftpd.log | tail -20

# Contar recusas do dia
grep "too many sessions" /var/log/vsftpd.log | grep "$(date '+%b %e')" | wc -l
```

---

## 5. Monitorar desempenho do servidor durante transferências

```bash
# CPU e iowait em tempo real (atualiza a cada 1 segundo)
watch -n 1 "top -bn1 | grep '%Cpu'"

# Memória disponível
watch -n 5 "free -m | grep Mem"

# Tudo junto: conexões + lojas concluídas (atualiza a cada 30 segundos)
watch -n 30 "echo 'Conexoes ativas:' && netstat -an | grep :21 | grep ESTABLISHED | wc -l && echo 'Lojas concluidas hoje:' && grep 'OK UPLOAD' /var/log/vsftpd.log | grep \"$(date '+%b %e')\" | grep -oP '::ffff:\K[\d.]+' | sort -u | wc -l"
```

---

## 6. Verificar arquivos na pasta de backups

```bash
# Tamanho total da pasta
du -sh /home/vip/ftp/files/backups/

# Quantidade de arquivos na pasta
ls /home/vip/ftp/files/backups/ | wc -l

# Acompanhar arquivos sendo recebidos em tempo real
watch -n 2 "ls -lh /home/vip/ftp/files/backups/ | tail -20"
```

---

## 7. Verificar uploads e deletes do dia

```bash
# Total de uploads do dia
grep "OK UPLOAD" /var/log/vsftpd.log | grep "$(date '+%b %e')" | wc -l

# Total de deletes do dia
grep "OK DELETE" /var/log/vsftpd.log | grep "$(date '+%b %e')" | wc -l

# Diferença entre uploads e deletes (arquivos ainda aguardando processamento)
echo "Uploads: $(grep 'OK UPLOAD' /var/log/vsftpd.log | grep "$(date '+%b %e')" | wc -l) | Deletes: $(grep 'OK DELETE' /var/log/vsftpd.log | grep "$(date '+%b %e')" | wc -l)"
```

---

## 8. Verificar status do Fail2Ban (proteção SSH)

```bash
# Status geral
sudo fail2ban-client status sshd

# IPs atualmente banidos
sudo fail2ban-client status sshd | grep "Banned IP"

# Acompanhar bans em tempo real
sudo tail -f /var/log/fail2ban.log | grep -E "Ban|Unban|Found"
```

---

## 9. Resumo completo do servidor (comando único)

```bash
echo "=== CONEXOES FTP ===" && \
netstat -an | grep :21 | grep ESTABLISHED | wc -l && \
echo "=== UPLOADS HOJE ===" && \
grep "OK UPLOAD" /var/log/vsftpd.log | grep "$(date '+%b %e')" | wc -l && \
echo "=== LOJAS UNICAS HOJE ===" && \
grep "OK UPLOAD" /var/log/vsftpd.log | grep "$(date '+%b %e')" | grep -oP '::ffff:\K[\d.]+' | sort -u | wc -l && \
echo "=== MEMORIA ===" && \
free -m | grep Mem && \
echo "=== DISCO ===" && \
df -h | grep -E "sda1|sdb1"
```

---

## 10. Referência rápida — Valores ideais

| Métrica | Saudável | Atenção | Crítico |
|---|---|---|---|
| iowait (wa) | < 20% | 20–40% | > 40% |
| Memória swap | 0% | qualquer uso | > 50% |
| Conexões FTP | < 80/100 | 90–100/100 | 100/100 |
| Espaço em disco | < 70% | 70–85% | > 85% |

---

## Configuração atual do vsftpd (/etc/vsftpd.conf)

```ini
max_clients=100
max_per_ip=3
local_max_rate=1000000
idle_session_timeout=300
data_connection_timeout=300
write_enable=YES
xferlog_enable=YES
pasv_min_port=40000
pasv_max_port=40100
```

Para aplicar alterações:
```bash
sudo systemctl restart vsftpd
sudo systemctl status vsftpd
```
