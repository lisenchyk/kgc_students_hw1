Task 1
Итак сначала я с помощью  FilleZilla   перекидываю резервную копию на сервер Далее создаю рабочие папки и рабочую группу sudo chown root:senior
И с помощью sudo chmod настраиваю права
sudo chmod 2770 
2  setegit
7 права владелеца rwx
7 права группы rwx
0 права для остальных
sudo chmod 1777
1 sticky bit разрешает удалять только свои файлы
7 права владелеца rwx
7 права группы rwx
7 права остальных rwx 
Далее с помощью  Split Разделяю файлы на отдельные раста файлы, используя в качестве разделителя строки с >
Далее с помощью цикла разделяю файлы по папкам по ключевым словам в заголовке, 

  sudo bash -c '
for file in fasta_*.fa; do
    if [ ! -f "$file" ]; then
        continue
    fi
    header=$(head -n1 "$file")
    if echo "$header" | grep -qiE "protein|peptide|orf"; then
        echo "Определен как белок: $file"
        mv -v "$file" protein/
    elif echo "$header" | grep -qiE "mrna|transcript|cdna|rna"; then
        echo "Определен как мРНК: $file"
        mv -v "$file" mrna/
    elif echo "$header" | grep -qiE "genom(e|ic)|chromosome|contig|scaffold|sequence|plasmid|complete|assembly"; then
        echo "Определен как геномная последовательность: $file"
        mv -v "$file" sequence/
    else
        echo "Неизвестный тип: $file"
        mv -v "$file" unknown/
    fi
done'
Далее с помощью gzip сжимаю файлы в gz



 ____________________________________
/ ура первое задание \
\ все!                            /
 ------------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
![second task all processes](/students/alinametiloranch//kgc_students_hw1/alina/<img width="1440" height="900" alt="screentop" src="https://github.com/user-attachments/assets/77616f89-3a7d-4de1-a254-6938569af890" />
screentop.png)
задние 2:
USER: root,  pid: 254603
если известен pid: kill номер_pid
(kill 254603)
если pid не известен, то можно использовать pkill (уничтожит все процессы в которых есть название которые мы впишем далее)
pkill java 
если процесс завис и не отвечает добавляем флаг -9, он принудительно завершает все процессы 
pkill -9 java 

PR (Priority) обозначает приоритет планирования в ядре linux. чем ниже число, тем выше приоритет 
NI (nice value) влияет на приоритет пользовательских процессов, диапазон -20 (наивысший) до 19 (низший)
0 нейтральный 
VIRT (virtual memory size) вся память, запрошенная процессом 
RES (Resident memory size) 
фактическая физ память (ram), используемая процессом 
SHR (Shared memory) часть RES, доступная для совместного использования 
S (process status)- текущее состояние процессора 
R выполняется 
S ожидает 
D не реагирует на сигналы 
Z завершен но родитель не обработал его статус 
T приостановлен сигналом
Сменить отображение занимаемых ресурсов в битах на мб, гб и тд в функции топ при нажатии клавиши E
Swap - область на диске, используемая когда системе не хватает RAM
когда ram заполняется, неактивные страницы памяти перемещаются в swap 
