## №1. Написание API-калькулятора и запуск в Docker-контейнере

Напишем API-калькулятор на python с помощью веб-фреймворка FastAPI с базовыми операциями.  
Пишем Dockerfile для запуска калькулятора в контейнере. Образ будет собираться на образе python:3.9-slim, будут устанавливаться зависимости, далее будет запущен калькулятор с помощью веб-сервера uvicorn.  

Протестируем работу локально:  
Соберем образ с помощью команды **docker build -t calculator .**  
![image](https://github.com/user-attachments/assets/93ae3c26-b446-4cf1-b739-45384a858aef)  

Запустим контейнер на порту 8000 и проверим, что он запустился:  
![image](https://github.com/user-attachments/assets/ce51af16-9cd0-4997-877a-5bf5c6229352)  

Проверим работу калькулятора с помощью curl-запросов:  
![image](https://github.com/user-attachments/assets/e8ed2560-35f2-4939-806a-a5b1393e4c9e)  

## №2,3. Создание пайплайна для обновления версии калькулятора и внедрение открытых инструментов безопасности в пайплайн  

Для выполнения задания был выбран gitlab.com. В Settings/CI-CD/Runners выбираем подключение раннеров для этого проекта:  
![image](https://github.com/user-attachments/assets/9bec1a75-81d8-4ee7-b16f-dc42e329b885)  
  
В пайплайне выполняется 4 стейджа. Первый этап - сканирование с помощью semgrep:  
![image](https://github.com/user-attachments/assets/41b56ffc-f02d-49ea-b54b-8ca31905a386)  
Сканирование выполняется автоматическим конфигом с сохранением отчета semgrep_report.json в артефакты. 

Сборка образа с помощью Docker-in-Docker с последующим пушем в Gitlab-Registry:  
![image](https://github.com/user-attachments/assets/d31e45b3-7537-4b17-b288-b74161950f72)  

После сборки образа выполняется его сканирование с помощью trivy:  
Устанавливается curl и trivy, далее происходит авторизация в регистри и сканирование с генерацией отчета в артефакты гитлаб.  
![image](https://github.com/user-attachments/assets/f8794c55-4191-4b5b-b49c-1a955dfeb2dc)  

Заключительным этапом является развертывание калькулятора и проверка curl-запросами:  
![image](https://github.com/user-attachments/assets/244735e8-8006-4802-948c-a6b8d5e3cbda)  

Отработка пайплайна:  
![image](https://github.com/user-attachments/assets/91a21e23-294e-480b-b8f5-f305cdac5194)

Образ в Gitlab Registry:  
![image](https://github.com/user-attachments/assets/aa4bba90-c89d-4bdc-b06d-d4e60102ea46)  

Результат выполнения этапа запуска контейнера и тестирования калькулятора:  
![image](https://github.com/user-attachments/assets/76c31890-6327-4ce6-98c3-076d7864c707)  

## №4. Анализ работы инструментов безопасности:  

**Semgrep**  
Отчет показал, что никаких уязвимостей не было найдено:  
![image](https://github.com/user-attachments/assets/03151a73-d01d-4b5e-aab6-0b2a10e5b269)  

**Trivy**  
В начала отчета представлен операционная система на которой построен образ, в данном случае это debian 12. Далее показаны хэши образа и его слоев:  
![image](https://github.com/user-attachments/assets/093e73da-5322-4421-b68c-e5984b984b7f)  




















