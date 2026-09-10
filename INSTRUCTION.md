# Інструкція із застосування маніфестів

Усі маніфести знаходяться в директорії `.infrastructure`.

## 1. Створення namespace

```bash
kubectl apply -f .infrastructure/namespace.yml
```

Перевірити, що namespace створено:

```bash
kubectl get namespace todoapp
```

## 2. Застосування маніфестів ToDo app та busybox

```bash
kubectl apply -f .infrastructure/todoapp-pod.yml -n todoapp
kubectl apply -f .infrastructure/busybox.yml -n todoapp
```

Перевірити стан подів (дочекатися статусу `Running` та `1/1 Ready`):

```bash
kubectl get pods -n todoapp -w
```

## 3. Тестування ToDo app через port-forward

Прокинути порт з пода на локальну машину (замініть `<port>` на порт, на якому працює застосунок, наприклад 8000):

```bash
kubectl port-forward pod/todoapp -n todoapp 8000:<port>
```

Після цього застосунок буде доступний локально:

```bash
curl http://localhost:8000/
```

Також можна перевірити readiness та liveness ендпоінти:

```bash
curl http://localhost:8000/api/health/ready
curl http://localhost:8000/api/health/live
```

Зупинити port-forward можна комбінацією `Ctrl+C`.

## 4. Тестування застосунку з контейнера busyboxplus

Зайти всередину пода busybox:

```bash
kubectl exec -it busybox -n todoapp -- sh
```

Виконати запит до ToDo app всередині кластера (звертаємось за IP пода або сервіс-іменем, якщо є Service):

```bash
curl http://<todoapp-pod-ip>:<port>/
curl http://<todoapp-pod-ip>:<port>/api/health/ready
curl http://<todoapp-pod-ip>:<port>/api/health/live
```

IP пода можна дізнатись командою:

```bash
kubectl get pod todoapp -n todoapp -o wide
```

Вийти з контейнера — команда `exit`.