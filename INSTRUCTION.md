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
kubectl apply -f .infrastructure/todoapp-pod.yml
kubectl apply -f .infrastructure/busybox.yml
```

Перевірити стан подів (дочекатися статусу `Running` та `1/1 Ready`):

```bash
kubectl get pods -n todoapp -w
```

## 3. Тестування ToDo app через port-forward

Прокинути порт з пода на локальну машину (застосунок слухає порт 8000):

```bash
kubectl port-forward pod/todoapp -n todoapp 8000:8000
```

Після цього застосунок буде доступний локально:

```bash
curl http://localhost:8000/
```

Перевірка readiness та liveness ендпоінтів:

```bash
curl http://localhost:8000/api/readiness/
curl http://localhost:8000/api/health/
```

Зупинити port-forward можна комбінацією `Ctrl+C`.

## 4. Тестування застосунку з контейнера busyboxplus

Зайти всередину пода busybox:

```bash
kubectl exec -it busybox -n todoapp -- sh
```

Дізнатися IP пода todoapp:

```bash
kubectl get pod todoapp -n todoapp -o wide
```

Виконати запит до ToDo app всередині кластера:

```bash
curl http://<todoapp-pod-ip>:8000/
curl http://<todoapp-pod-ip>:8000/api/readiness/
curl http://<todoapp-pod-ip>:8000/api/health/
```

Вийти з контейнера — команда `exit`.