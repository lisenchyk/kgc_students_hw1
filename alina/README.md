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
![second task all processes](/students/alinametiloranch/screentop.png)
