# Решение задачи 1

## Выполненные действия:
### 1.	Восстановление структуры директорий 
- Войти на сервер
- Сделать fork из репозитория glitchheadgit на свой lisenchyk, скопировать его ссылку и перекинуть на сервер  
``git clone https://github.com/lisenchyk/kgc_students_hw1.git``  
- Создаю папку work в рабочей папке и туда перегружаю скопированную ссылку 
- Создать индикатор для работы над файлом -lisenchyk (заходим в эту папку форка, создаем новую ветку, делаем в терминале папку со своим именем и делаем все дз в ней. Создаем ридми файл и пишем туда как, выполнялась работа) 
``git checkout -d lisenchyk``  
``git branch``  
(Должно перейти с main на *lisenchyk) Далее:   
``mkdir lisenchyk``  
``cd /lisenchyk``  
``touch README.md``  
- Оформить структуру директории:
Создаем структуру папок в username - lisenchyk:  
``mkdir -p Task1/{protein,mrna,reference,work}``  
Username  
1. Task1  
- protein  
- mrna  
- eference  
- work
2. README.md  
### 2.  Распаковка и классификация FASTA-файлов
-  Скачать архив dump.fasta.gz и переместить на сервер    
-  Переместить dump.fasta.gz в папку (Task1)  
``cd /students/lisenchyk/work/kgc_students_hw1/lisenchyk/Task1``  
``cp /students/lisenchyk/work/dump.fasta.gz .``  
-  Распаковать dump.fasta.gz в эту папку (Task1)  
``gunzip -c ./dump.fasta.gz > dump.fasta``  
-  Переходим в Task1 для обработки   
``cd Task1``  
Чтобы просмотреть начало файла (первые 20 строк):  
``head -n 20 dump.fasta``  
Просмотреть конец файла (последние 20 строк):  
``tail -n 20 dump.fasta``  
Просмотр первых 10 заголовков:  
``grep '>' dump.fasta | head -n 10``
Просмотр всех заголовков:   
``grep '>' dump.fasta``   
Просмотр строк с 'complete sequence':   
``grep 'complete sequence' dump.fasta``  
и т.д.  
``grep 'protein' dump.fasta``   
``grep 'mRNA' dump.fasta``   
``grep 'complete genome' dump.fasta``  
Счёт (-c  позволяет считать, сколько по условию заголовков):  
``grep -c '>' dump.fasta``  
(133)  
Итого:  
'complete sequence' - 39  
'protein' - 46  
'mRNA' - 19  
'complete genome' - 37  
-  Разделить FASTA-файла на отдельные файлы  
Мы используем команду `awk` для разделения большого FASTA-файла на отдельные файлы, каждый из которых содержит одну последовательность.  
``awk 'BEGIN {n=0}``   
``/^>/ {``  
``if (n) close(outfile)``  
``n++``  
 ``outfile = sprintf("fasta_%03d.fa", n)``  
``print $0 > outfile``  
``next``  
``}``  
``{print $0 > outfile}' dump.fasta``  
Пояснение:  
Инициализируем переменную `n` (счетчик последовательностей) нулем.  
`/^>/ { ... }` - этот блок выполняется для каждой строки, которая начинается с символа `>` (т.е. для заголовка последовательности).  
`if (n) close(outfile)` - если `n` не равно нулю (т.е. это не первая последовательность), закрываем предыдущий файл.  
`n++` - увеличивает счетчик последовательностей на 1.  
`outfile = sprintf("fasta_%03d.fa", n)` - формирование имя выходного файла с помощью функции `sprintf`.  
`%03d` - означает целое число (`d`), так чтобы общая длина была 3 символа.  
`print $0 > outfile` - печатает текущую строку (которая является заголовком) в новый файл.
`$0` - означает всю текущую строку.  
`next` - переходим к следующей строке.  
`{print $0 > outfile}` - этот блок выполняется для всех строк, которые не являются заголовками.Далее печатаем текущую строку в текущий открытый файл dump.fasta.  
-  Классифицировать файлы по папкам (protein/mrna/reference)
Теперь циклом `for` распределяем файлы по папкам:  
``for file in*; do …;``   
``done``  
Полный цикл:  
``for file in fasta_*.fa; do``  
``header=$(head -n1 "$file")``  
``if [[ "$header" == *"complete genome"* ]] || ``  
``[[ "$header" == *"complete sequence"* ]]; then``  
``mv -v "$file" reference/``  
``elif [[ "$header" == *"protein"* ]]; then``  
``mv -v "$file" protein/``  
``elif [[ "$header" == *"mRNA"* ]]; then``  
``mv -v "$file" mrna/``  
``else``  
``mv -v "$file" work/``  
``fi``  
``done``  
Пояснение:
Цикл `for` перебирает все файлы с шаблоном имени `fasta_*.fa` в текущей директории. Пока файлы в `fasta_*.fa` - `do`.
В каждом файле считываем первую строку и сохраняем в переменную header, далее если `header` содержит подстроку `complete genome` или `complete sequence`, то перемещаем файл в папку `reference/`. Аналогично для `protein`, `mRNA`. Если ни одно условие не подошло, перемещаем файл в папку `work/`.  
-  Создать файлы: со сводной статистикой (Task1/summary.txt),  с размерами последовательностей (Task1/sizes_report.txt)
Для вычисления длины последовательностей (количество нуклеотидов/аминокислот) используем:
Функция для вычисления длины последовательности в файле:  
``calculate_length() {``  
``file=$1``  
``grep -v '^>' "$file" | tr -d '\n' | wc -c`` - удаляет заголовки и переводы строк, считает символы  
``}``   
Создаем отчет о размерах (Task1/sizes_report.txt):  
``echo "Размеры последовательностей:" > sizes_report.txt``  
``echo "Белки:" >> sizes_report.txt``  
``for f in protein/*.fa; do``  
``length=$(calculate_length "$f")``  
``echo "$(basename $f): $length аминокислот" >> sizes_report.txt``  
``done``  
``echo -e "\nмРНК:" >> sizes_report.txt``  
``for f in mrna/*.fa; do``  
``length=$(calculate_length "$f")``  
``echo "$(basename $f): $length нуклеотидов" >> sizes_report.txt``  
``done``  
``echo -e "\nРеференсные геномы:" >> sizes_report.txt``  
``for f in reference/*.fa; do``  
``length=$(calculate_length "$f")``  
``echo "$(basename $f): $length п.н." >> sizes_report.txt``  
``done``  
``echo -e "\nРабочие файлы:" >> sizes_report.txt``  
``for f in work/*.fa; do``  
``length=$(calculate_length "$f")``  
``echo "$(basename $f): $length символов" >> sizes_report.txt``  
``done``  
Создаем отчет о статистике (Task1/summary.txt):  
``echo "Сводная статистика:" > summary.txt``  
``echo "Белки: $(ls protein | wc -l) файлов" >> summary.txt``  
``echo "мРНК: $(ls mrna | wc -l) файлов" >> summary.txt``  
``echo "Референсные геномы: $(ls reference | wc -l) файлов" >> summary.txt``  
``echo "Рабочие файлы: $(ls work | wc -l) файлов" >> summary.txt``  
``echo "Общее количество: $(ls {protein,mrna,reference,work} | wc -l) файлов" >> summary.txt``
Просмотр результатов отчетов:  
``cat sizes_report.txt``  
``cat summary.txt``  
### 3.  Настройка прав доступа
-  Настроить права доступа для папок protein, mrna, reference: владелец и группа – полные права; остальные – чтение и запуск:  
``chmod -R 775 protein mrna reference``  
`-R` — рекурсивно, то есть изменения применяются ко всем файлам и поддиректориям внутри указанных директорий.  
`775` — это восьмеричное представление прав доступа:  
Первая цифра (7) — права для владельца (user): 7 = 4 (read) + 2 (write) + 1 (execute) = полные права.  
Вторая цифра (7) — права для группы (group): аналогично, полные права.  
Третья цифра (5) — права для остальных (others): 5 = 4 (read) + 1 (execute) = чтение и выполнение.  
-  Настроить права доступа для папки work: общедоступная, но с защитой от удаления файлов:  
``chmod 1777 work``  
`1` — это установка "sticky bit".  
`777` — полные права для всех: владельца, группы и остальных (чтение, запись, выполнение).  
`sticky bit` (1) — это специальный флаг для директорий. Он означает, что удалить или переименовать файл внутри этой директории может только владелец файла, владелец директории или суперпользователь.  
### 4.  Сжатие результатов в .gz  
``gzip protein/*.fa``  
``gzip mrna/*.fa``  
``gzip reference/*.fa``  
``gzip work/*.fa``  
### 5.  Сохранение истории команд в History.txt  
``history > History.txt``  
### 6.  Фиксация изменений в Git






























