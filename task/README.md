# Коммутатор учится: source MAC → FDB → egress port

В отличие от Lab 003, где мы изучили поля Ethernet II, в Lab 004 изучаем решение **самого L2-коммутатора**.

`pc1:eth1 ↔ sw1:eth1`, `pc2:eth1 ↔ sw1:eth2`, `pc3:eth1 ↔ sw1:eth3`. `pc3` — наблюдатель: он фиксирует flooded кадр, адресованный `pc2`.

Эксперименты:
1. Заранее задаём адреса и static neighbor entries на хостах, чтобы ARP не скрывал работу switch. MAC хостов фиксированы в topology.
2. Очищаем **только dynamic FDB** на bridge, посылаем первый unknown-unicast ICMP и сохраняем capture с `pc3:eth1`.
3. После ответного кадра ищем `MAC pc1 → eth1` и `MAC pc2 → eth2` в FDB.
4. Повторяем unicast и сравниваем capture наблюдателя: после обучения кадры для pc2 уже не flood-ятся на `eth3`.
5. Получаем первый CHECK, затем отключаем `sw1:eth2` **от br0**, сохраняя линк PC1↔PC3.
6. Диагностируем отказ PC2 при рабочем PC3, возвращаем eth2 в br0, проверяем восстановление.

Трафик настоящий; capture через `%%capture_traffic`, статические таблицы через `PcapViewer(...).packets()`. Все ответы студентов проверяются только по смыслу опциональным REVIEW-ONLY; программный CHECK проверяет observable state.

`eth0` у всех контейнеров — management plane платформы. Не изменяем.
