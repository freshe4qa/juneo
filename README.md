<p align="center">
  <img height="100" height="auto" src="https://github.com/freshe4qa/juneo/assets/85982863/0efcf567-b5c6-49e5-a728-365249789f07">
</p>

# Juneo - Socotra Testnet

Official documentation:
>- [Guide](https://docs.juneo.com/intro/build/node-requirements)

Explorer:
>- [Explorer](https://socotra.mcnscan.io)

### Minimum Hardware Requirements
 - 4x CPUs; the faster clock speed the better
 - 8GB RAM
 - 160GB of storage (SSD or NVME)
 - Ubuntu 20.04

# Синхронизация даты и времени

Мы рекомендуем правильно синхронизировать время вашего узла.

Чтобы включить синхронизацию времени в Linux, выполните команду ``timedatectl`` в терминале.

Если параметр службы NTP установлен в значение no, то время вашего узла может не синхронизироваться. Настройте синхронизацию времени, выполнив команду:

``timedatectl set-ntp on``

Запустите ``timedatectl`` еще раз, и параметр System clock synchronized должен теперь иметь значение yes.

# Настройка и подключение узла к Socotra Testnet

Для начала необходимо перенести файлы проекта, найденные здесь, на ваш сервер. Если на вашем сервере установлен git, вы можете выполнить следующие команды:

`sudo apt update && sudo apt upgrade -y`

``apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y``

``cd $HOME``

``git clone https://github.com/Juneo-io/juneogo-binaries``

Теперь все необходимые файлы будут находиться в папке juneogo-binaries в вашем домашнем каталоге.

# Настройка исходных двоичных файлов

Не выполняйте скрипты preparation.sh и simple_setup.sh, находящиеся в директории juneogo-binaries. Они предназначены только для использования при выполнении руководства по установке скрипта JuneoGo.

Для запуска JuneoGo необходимы следующие двоичные файлы:

1) juneogo

2) jevm

3) srEr2XGGtowDVNQ6YgXcdUb16FGknssLTGUFYg7iMqESJ4h8e

Чтобы предоставить права на выполнение двоичных файлов, выполните следующие команды:

``chmod +x ~/juneogo-binaries/juneogo``

``chmod +x ~/juneogo-binaries/plugins/jevm``

``chmod +x ~/juneogo-binaries/plugins/srEr2XGGtowDVNQ6YgXcdUb16FGknssLTGUFYg7iMqESJ4h8e``

После этого двоичный файл juneogo следует переместить в домашний каталог. Оставшиеся два двоичных файла следует переместить в каталог ~/.juneogo/plugins.

Для этого выполните следующие команды:

``mv ~/juneogo-binaries/juneogo ~``

``mkdir -p ~/.juneogo/plugins``

``mv ~/juneogo-binaries/plugins/jevm ~/.juneogo/plugins``

``mv ~/juneogo-binaries/plugins/srEr2XGGtowDVNQ6YgXcdUb16FGknssLTGUFYg7iMqESJ4h8e ~/.juneogo/plugins``

Структура вашего домашнего каталога должна выглядеть следующим образом:

├── juneogo
├── .juneogo/
│   ├── plugins/
│   │   └── jevm
│   │   └── srEr2XGGtowDVNQ6YgXcdUb16FGknssLTGUFYg7iMqESJ4h8e

Если эти файлы имеют структуру, отличную от указанной выше, вы не сможете подключить узел.

Теперь вы можете подключить узел к сети:

sudo tee /etc/systemd/system/juneod.service > /dev/null << EOF
[Unit]
Description=Juneo node service
After=network-online.target
[Service]
User=root
ExecStart=/root/juneogo start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

``sudo systemctl daemon-reload``

``sudo systemctl enable juneod.service``

``sudo systemctl start juneod.service``

Это приведет к получению блоков и загрузке узла.

Вы можете проверить, загрузился ли узел, с помощью следующего вызова:

curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"info.isBootstrapped",
    "params": {
        "chain":"JUNE"
    }
}' -H 'content-type:application/json;' 127.0.0.1:9650/ext/info

Ожидаем значения true, после чего следуем далее:

{
  "jsonrpc": "2.0",
  "result": {
    "isBootstrapped": true
  },
  "id": 1
}

# Добавление узла в набор валидаторов

Как добавить узел JUNEO в качестве валидатора в первичную сеть.

После успешной загрузки узла вы можете добавить его в качестве валидатора в первичную сеть (после чего вы сможете создавать суперсети и разворачивать на них блокчейн).

# Ставки и сборы

Минимальная ставка в Mainnet составляет 100 JUNE, а максимальная - 30 000 JUNE. На Socotra Testnet минимальная ставка составляет 1 JUNE, а максимальная - 30 000 JUNE. Вам также понадобятся дополнительные средства для оплаты комиссии за транзакции.

Минимальный период ставки составляет 14 дней в Mainnet и Socotra Testnet. Максимальный период ставок в обеих сетях составляет 365 дней.

Любой делегат, делающий ставку на вашу ноду, будет платить вам комиссию за делегирование, которая составляет 12% от его вознаграждения. Делегаты могут делать ставки не более чем на x4 от вашей собственной ставки.

Узел не может быть валидатором в суперсети дольше, чем он является валидатором в основной суперсети. Если вы хотите развернуть суперсеть и добавить свой узел в качестве валидатора, мы рекомендуем делать стейкинг в основной суперсети не менее 15 дней. После того как вы застолбите свои токены, вы сможете развернуть суперсеть и добавить в нее свой узел в качестве валидатора на минимально необходимый период валидации.

# Сеть

Для работы узла валидатора требуется пропускная способность не менее 5 Мбит/с (как на загрузку, так и на выгрузку).

Узел должен принимать соединения из Интернета по сетевым портам 9651 (и, опционально, по порту 9650 для разрешения удаленных вызовов RPC). В Linux используйте брандмауэр ufw:

``sudo ufw allow 9650``

``sudo ufw allow 9651``

Не рекомендуется запускать узел за NAT. Установка в домашних условиях не рекомендуется, кроме как в целях тестирования.

Если вы запускаете узел на домашнем компьютере, то, скорее всего, у вас динамический IP. Вам нужно будет либо попросить провайдера назначить постоянный публичный адрес, либо настроить проброс порта 9651 (и, по желанию, 9650) из Интернета на компьютер узла. Пожалуйста, обратитесь к документации по маршрутизатору.

Активный узел поддерживает тысячи живых TCP-соединений, имейте это в виду, выбирая маршрутизатор для такой установки. Дешевый маршрутизатор может вызвать проблемы с сетью и заставить ваш узел считаться оффлайновым из-за плохого соединения. Нахождение в сети менее 80 % времени действия ставки означает потерю вознаграждения.

# Добавление валидатора с помощью графического интерфейса

Первый шаг к получению статуса валидатора - это получение идентификатора узла и подготовка информации о ключе BLS, включающей открытый ключ и доказательство владения им. Откройте терминал на сервере, на котором работает ваш узел, и выполните следующий вызов:

curl -X POST --data '{
    "jsonrpc":"2.0",
    "id"     :1,
    "method" :"info.getNodeID"
}' -H 'content-type:application/json' 127.0.0.1:9650/ext/info

Пример ответа:

{
  "jsonrpc": "2.0",
  "result": {
    "nodeID": "NodeID-4JfgcoMWBpxCQL5VmyQ1f6L36mUbLLBga",
    "nodePOP": {
      "publicKey": "MyBLSKey",
      "proofOfPossession": "MyBLSSignature",
    }
  },
  "id": 1
}

Затем скопируйте значения nodeID, publicKey и proofOfPossession из ответа и войдите в mcnwallet.io.

Пожалуйста, убедитесь, что у вас есть баланс не менее 1 JUNE в P-цепочке, а также дополнительные JUNE для оплаты транзакций. Для этого вы можете совершать межцепочечные транзакции, чтобы перевести JUNE из любой другой цепи в P-цепь на странице "Межцепочечные транзакции".

Откройте тикет в Discord(https://discord.gg/2pFR8fcm) 🚰│faucet
Предоставьте Node-ID и адрес кошелька, Вам отправят тестовые токены.

В кошельке mcnwallet перейдите на страницу Stake и нажмите на кнопку Validate card. Введите свой NodeID, BLS-ключ (publicKey) и BLS-подпись (proofOfPossession). Установите сумму ставки 1 JUNE, а период валидации - 14 дней. Убедитесь, что вся информация введена правильно, чтобы ваш узел был допущен к проверке.

![image](https://github.com/freshe4qa/juneo/assets/85982863/197b167b-4410-4b24-914a-11279112d3a7)

Затем нажмите Validate, чтобы добавить ваш узел в набор Validator.

После завершения этой операции вы можете перейти на сайт https://mcnscan.io/validator/validator-list и ввести идентификатор узла в строку поиска, что покажет вам статус вашего узла:

![image](https://github.com/freshe4qa/juneo/assets/85982863/b5915079-41a8-4c4e-bda5-6007504eb35b)
