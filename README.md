# 💳 Cheatsheet on most common Strip products

NB: All the information below was taken from official Stripe documentation.

Summary:
-	Payments API - прием платежей с карт, including recurring payments (subscriptions) and deferred payments (e.g. for rental use cases) 
-	Connected accounts - выплаты на банковские счета клиентов (IBAN)
-	Atlas - юрлицо в Дэлавере (США, низкие налоги) под ключ за несколько дней. Юрлицо и счет в банке открывается за несколько дней. Налоговый номер выдаётся за 1-2 недели.  

# Deferred payments
## Payment intents 
Намерения о платеже. Позволяют разделять авторизацию денег на счету (проверка что денег достаточно для транзакции и hold’ирование суммы чтобы её нельзя было использовать) и само списание денег (когда они списываются с карты). Механизм включает в себя проверку 3DS и возможность списания денег в интервале до 7 дней (называется «separate auth/capture» в документации). 
После 7 дней можно отменять payment intent (через cancel payment intent) и сразу же пересоздавать его заново. Таким образом можно реализовать аренду.  
## Setup intents 
Отличие от payment intent - можно один раз авторизовывать деньги (и проходить 3DS а списывать хоть через год. Но есть риск что через год у кардхолдера не будет достаточно денег (тогда страйп будет несколько раз пытаться списать и будет слать уведомления) или карта истекла или заблокирована. 
## Payment methods 
Кроме карт поддерживаются европейские местечковые платежки вроде iDEAL, Bancontact. То есть клиент может заплатить не картой а платёжкой (флоу немного другой, иногда требуется ввести код на портативном терминале вставив карту). 
 
# Подписки 
Обычные рекуррентные платежи. Используются payment intents. В теории можно сделать двухстадийную оплату (то есть чтобы при подписке деньги авторизовывались на счету но не списывались сразу). 
Если при оплате подписки на карте нет денег - то страйп будет несколько раз пытаться списать и будет слать уведомления (сколько раз будет пытаться и как часто - настраивается в Stipe Dashboard, аля админке). 

# Payouts (Выплаты) 
Чтобы выводить деньги пользователям на банковские счета. Есть standard, express и custom аккаунты. 
Standard - бесплатный, но клиенту по сути надо регистрировать в страйпе свою учетку (относительно долго и относительно муторно). 
Express - дорогой, отличие от стандарта что учетка создается автоматически (быстрее и проще клиенту)
Custom - дорогой, отличие от экспресса что фронтенд можно кастомизировать 

# Restrictions (Ограничения)
## Strong Customer Authentication (SCA) 
Уже имеется в Страйпе. По сути это 3DS - при оплате пользователь вводит OTP на странице банка в качестве обязательного доп. подтверждения. 
В версии 3DS 2.0 в большинстве случаев юзеру даже не надо вводить OTP. 
## PCI DSS 
При интеграции со Страйпом чтобы выполнить требований безопасности данных карт - достаточно: 
-	SSL/TLS сертификат
-	отсутствие уязвимостей по спискам (проверяется с помощью SonarQube):
o	OWASP 
o	SANS 
o	NIST 
-	После релиза на продакшен (не позже чем через 2 месяца) - заполнить чеклист SAQ A
![image](https://github.com/banonymous/Stripe-Cheatsheet/assets/28014318/3458303d-9a23-42d5-b5d8-763873f987fd)
