# Задачи по Fortran

## Задача #1

#### _Задание:_
Дана квадратная матрица. Проверить положительна ли сумма под главной диагональю. Если да, то найти над главной диагональю максимальный четный элемент.

---
#### _Логика:_
Пройтись под дигональю для поиска суммы и над диагональю для поиска миксимального четного. Рассмотрим матрицу:
| 1  | 2  | 3  | 4  |
|----|----|----|----|
| 5  | 6  | 7  | 8  |
| 9  | 10 | 11 | 12 |
| 13 | 14 | 15 | 16 |

Главная диагональ проходит из левого верхнего угла в правый нижний. Индексы по строкам и столбцам у этих элементом совпадают.
Индексы главной диагонали:
|  i  |  1  |  2  |  3  |  4  |
|-----|-----|-----|-----|-----|
|**j**|**1**|**2**|**3**|**4**|

Если **j > i**, то элемент находится выше главной диагонали.
Если **j < i**, то элемент находится ниже главной диагонали.

Для прохода по всем элементам выше главной диагонали необходимо использовать вложенный цикл **for**.\
Для **i** необходимо пройтись от **1** до **n - 1**, где n - размер матрицы. Последнюю строку можно не брать, справа от диагонали тут ничего нет.
Для **j** необходимо пройтись от **i + 1** до **n**, где n - размер матрицы.

Проход по всем элементам выше диагонали выглядит следующим образом:
```fortran
do i = 1, n - 1
  do j = i + 1, n
    ! Обработка элемента matrix(i, j)
  end do
end do
```

Для прохода по всем элементам ниже главной диагонали необходимо использовать вложенный цикл **for**.\
Для **i** необходимо пройтись от **2** до **n**, где n - количество строк. Первую строку можно не брать, слева от диагонали тут ничего нет.
Для **j** необходимо пройтись от **1** до **i - 1**, ибо элемент с индексом **i** - это элемент диагонали.

Проход по всем элементам ниже диагонали выглядит следующим образом:
```fortran
do i = 2, n
  do j = 1, i - 1
    ! Обработка элемента matrix(i, j)
  end do
end do
```

С индексами разобрались. Рассмотрим алгоритм поиска суммы. Необходимо просто пройтись под диагональю матрицы вложенным циклом и прибавлять каждый элемент матрицы к сумме.
Это выглядит следующим образом:
```fortran
integer :: s = 0
do i = 2, n
  do j = 1, i - 1
    s = s + matrix(i, j)
  end do
end do
```

Для поиска максимального четного элемента надо сделать похожие действия. Пройтись по матрице, сравнивать каждый элемент с текущим максимальным и проверять его на четность. *Изначально максимальное значение должно быть очень маленьким. Настолько маленького элемента не должно быть в матрице.*
```fortran
integer :: maxvalue = -100000
do i = 1, n - 1
  do j = i + 1, n
    if (matrix(i, j) > maxvalue .and. mod(matrix(i, j), 2) == 0) then
      maxvalue = matrix(i, j)
    end if
  end do
end do
```

---
#### _Решение:_
```fortran
program heh
    integer, parameter :: nmax = 100
    integer :: infile = 1, outfile = 2
    integer n, i
    integer A(nmax, nmax)
    integer s, maxvalue
    
    open(infile, file = 'in.txt')
    open(outfile, file = 'out.txt')
    
    call inMatrix(infile, A, n, n)
    
    call getSum(A, n, n, s)
    if (s > 0) then ! сравнение суммы с нулем
        call getMax(A, n, n, maxvalue)
        if (maxvalue /= -10000) then
            write(outfile, *) maxvalue
        else
            write(outfile, *) "Такого элемента в матрице нет"
        end if
    else
        write(outfile, *) "Сумма элементов под диагональю отрицательная" 
    end if
    
    close(infile)
    close(outfile)
    contains
        subroutine inMatrix(file, matrix, row, col)
            integer file, row, col, i, j
            integer matrix(nmax, nmax)
            read(file, *) row
            read(file, *) col
            read(file, *) ((matrix(i,j), j = 1, col), i = 1, row)
        end subroutine inMatrix

        subroutine getSum(matrix, row, col, s)
            integer row, col, i, j
            integer matrix(nmax, nmax), s
            s = 0
            do i = 2, row
                do j = 1, i - 1 ! цикл For от 1 до i - 1
                    s = s + matrix(i, j)
                end do
            end do
        end subroutine getSum
        
        subroutine getMax(matrix, row, col, m)
            integer row, col, i, j
            integer matrix(nmax,nmax), m
            m = -10000
            do i = 1, row - 1
                do j = i + 1, col
                    if (matrix(i, j) > m .and. mod(matrix(i, j), 2) == 0) then
                        m = matrix(i, j)
                    end if
                end do
            end do
        end subroutine getMax
end program heh
```
