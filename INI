
#!/bin/bash

SOURCE="/mnt/backup/"
DESTINATION="/mnt/meu_disco"
TELEGRAM_BOT_TOKEN="SEU TOKEN AQUI"
TELEGRAM_CHAT_ID="SEU ID AQUI" # Seu ID de chat privado
TELEGRAM_GROUP_CHAT_ID="-SEU ID GRUPO AQUI " # ID do chat do grupo
LOG_FILE="/var/log/backup.log"

# Verifica se os diretórios existem
if [ ! -d "$SOURCE" ]; then
    echo "Erro: Diretório de origem não encontrado: $SOURCE"
    echo "Erro: Diretório de origem não encontrado: $SOURCE" >> "$LOG_FILE"
    exit 1
fi

if [ ! -d "$DESTINATION" ]; then
    echo "Erro: Diretório de destino não encontrado: $DESTINATION"
    echo "Erro: Diretório de destino não encontrado: $DESTINATION" >> "$LOG_FILE"
    exit 1
fi

# Verifica se o destino do backup está montado
if ! mountpoint -q "$DESTINATION"; then
    MENSAGEM="Erro: o destino do backup ($DESTINATION) não está montado."
    echo "$MENSAGEM"
    echo "$MENSAGEM" >> "$LOG_FILE"
    curl -s -X POST "https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/sendMessage" -d chat_id="$TELEGRAM_CHAT_ID" -d text="$MENSAGEM" > /dev/null 2>&1
    curl -s -X POST "https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/sendMessage" -d chat_id="$TELEGRAM_GROUP_CHAT_ID" -d text="$MENSAGEM" > /dev/null 2>&1
    exit 1
fi

# Envia mensagem de início do backup para o Telegram
DATA_HORA_INICIO=$(date +"%Y-%m-%d %H:%M:%S")
MENSAGEM="Iniciando backup em $DATA_HORA_INICIO..."
echo "$MENSAGEM"
echo "$MENSAGEM" >> "$LOG_FILE"
curl -s -X POST "https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/sendMessage" -d chat_id="$TELEGRAM_CHAT_ID" -d text="$MENSAGEM" > /dev/null 2>&1
curl -s -X POST "https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/sendMessage" -d chat_id="$TELEGRAM_GROUP_CHAT_ID" -d text="$MENSAGEM" > /dev/null 2>&1

# Sincroniza os diretórios usando rsync com barra de progresso e registra a saída no log
rsync -av --progress --delete --ignore-errors "$SOURCE" "$DESTINATION" >> "$LOG_FILE" 2>&1

# Verifica o código de saída do rsync
DATA_HORA_FIM=$(date +"%Y-%m-%d %H:%M:%S")
if [ $? -eq 0 ]; then
    TAMANHO_DADOS=$(du -sh "$DESTINATION" | awk '{print $1}')
    MENSAGEM="Backup concluído com sucesso em $DATA_HORA_FIM. Tamanho dos dados: $TAMANHO_DADOS. Finalizando..."
else
    MENSAGEM="Backup concluído com erros em $DATA_HORA_FIM. Detalhes no log: $LOG_FILE. Finalizando..."
fi

# Envia mensagem de conclusão do backup para o Telegram
echo "$MENSAGEM"
echo "$MENSAGEM" >> "$LOG_FILE"
curl -s -X POST "https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/sendMessage" -d chat_id="$TELEGRAM_CHAT_ID" -d text="$MENSAGEM" > /dev/null 2>&1
curl -s -X POST "https://api.telegram.org/bot$TELEGRAM_BOT_TOKEN/sendMessage" -d chat_id="$TELEGRAM_GROUP_CHAT_ID" -d text="$MENSAGEM" > /dev/null 2>&1

exit 0
