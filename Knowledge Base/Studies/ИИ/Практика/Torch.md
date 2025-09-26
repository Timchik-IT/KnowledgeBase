PyTorch — это библиотека машинного обучения с открытым исходным кодом и фреймворк для глубокого обучения на Python, разработанный Meta AI.


### Создание [[Тензор|тензора]].
``` py
T = torch.Tensor(1, 2, 3)
print(T)
T = torch.empty(1, 2, 3, dtype=torch.int16)
print(T)
d = torch.tensor([[1, 2], [1, 2]], dtype=torch.int16)
print(d)
```

### Спец методы.
``` py
d=[[[1,2],[1,2],[3, 5]]]
T=torch.tensor(d,dtype=torch.float32)
T.type()
T.dim() #количество осей
T.size() #размер каждого измерения T.shape
```

### Импорт из numpy
``` py 
d_np=np.array([[1,2],[1,2],[3, 5]])
print(d_np)
print(d_np.dtype)

T2=torch.from_numpy(d_np) #сохраняет тип данных из NumPy
print(T2.dtype)


T3=torch.tensor(d_np)
print(T3.dtype)


d_np[0,0]=22 #связаны, если меняем 1, то меняется и 2, используют одни и те же данные
print(d_np)
print(T2)

T3[0,0]=11 # просто копирует
print(d_np)
# Со списком такого не прокатит

d=T2.numpy() #обратно в numpy
```

### Нулевой тензор
``` py
X = torch.zeros(2, 3)
print(X)
```

### Единичный тензор
```
X = torch.ones(3, 3)
print(X)
```

### Тензор с единицами по диагонали
```
X = torch.eye(3)
print(X)
X = torch.eye(3,2)
print(X)
```

### Методы манипуляции с тензорами
``` py 
#Методы изменяющие текущий тензор inplace, "имя_"
torch.IntTensor(2,5).zero_() #
print(torch.IntTensor(2,5).zero_())

#Методы не изменяющие текущий тензор, а формируют новый "имя"
torch.arange(7)

#Пример
X=torch.randn(2,3)
print(X)
X.fill_(1)
print(X)

x=torch.arange(7)
x.random_(1,7)
print(x)

#Тензоры можно решейпить с разным представлением
X=torch.arange(27)
d=X.view(3,9)
print(d)

X[0]=33
d=X.view(3,9)
print(d)

r=d.reshape(3,3,3) #и d, и X, и r будут работать с одними и теми же данными

print(r)


#Добавление и удаление осей
x_test=torch.arange(32).view(8,2,2)
print(x_test.size())

x_test4=torch.unsqueeze(x_test,dim=0) #-1 последняя ось
print(x_test4.size())

a=x_test4.squeeze() #-1 последняя ось
print(a.size())


#Индексы и срезы, как со списками
x=torch.arange(8)
x[1]
x[1].item()

print(x[::2])

#списочная индексация: формирование нового тензора на основе старого
a=torch.arange(8)
b=a[[0,3,-1]]
print(b)

#"Фильтрация"
b=a>2
print(a[b])


```


### Базовые операции

``` py 
X = torch.ones(2,3)
Y = torch.ones(2,3) * 2
print(X + Y)

Y[:,1] = Y[:,1] + 1
print(Y)
```

``` py 
a = torch.tensor([1, 2, 3, 1]).view(2, 2)
b = torch.tensor([4, 5])
print(a + b)
print(a * b)
```

``` py 
a=torch.arange(8,dtype=torch.float32).view(2,2,2)
#print(a.sum())
#print(a.max())
#print(a.min())
#print(a.mean())

print(a.sum(dim=0)) #через dim направление суммирования
a=torch.arange(8).view(2,4)
print(a.max(dim=1))
```

#### Поиск максимального значения и его индекса
``` py 
random_tensor=torch.arange(8).view(2,4)
max_val, max_idx = torch.max(random_tensor, dim=1)
print("Max values:", max_val)
print("Indices:", max_idx)
```

#### Нормализация
``` py 
b = torch.tensor([3, 4], dtype=torch.float32)
(находим длину/норму) (аналог np.linalg.norm())
norm = torch.norm(b)
print(norm)
```

#### Объединение тензоров (аналог concatenate)
``` py
a = torch.tensor([[1, 2], [3, 4]])
b = torch.tensor([[5, 6], [7, 8]])
print(torch.cat((a, b), dim=0))  # Склеивание по строкам
print(torch.cat((a, b), dim=1))  # Склеивание по столбцам
r=torch.vstack([a,b])
print(r)
```

#### Векторно-матричное умножение (аналог np.dot())
``` py 
mat1 = torch.tensor([[1, 2], [3, 4]])
mat2 = torch.tensor([[5, 6], [7, 8]])
print(torch.matmul(mat1, mat2)) # есть возможность транслирования
print(torch.mm(mat1, mat2)) # нет возможности транслирования
c=mat1.mm(mat2)
```

#### Умножение пакетов матриц
``` py 
bx=torch.randn(7,3,5) #внутри семь матриц 3х5 умножаем на 7 матриц 3х4
by=torch.randn(7,5,4)

bc=torch.bmm(bx,by)
print(bc.size())
```

#### Элементы ЛинАла
``` py 
a=torch.randn(9,dtype=torch.float32).view(3,3)
print(torch.linalg.matrix_rank(a)) #ранг матрицы
y=torch.FloatTensor([10, 11, 13])
print(torch.linalg.solve(a,y))

invA=torch.linalg.inv(a)
print(torch.matmul(invA,y.t()))
```





















