# Lab 2 — Version Control & Advanced Git

## Task 1 — Модель объектов Git (blob/tree/commit)
```
    PS C:\Users\amust\DevOps-Intro> git log --oneline -1
    a8790d5 (HEAD -> feature/lab2) Add test file
```

```
    PS C:\Users\amust\DevOps-Intro> git cat-file -p HEAD
    tree 18991419ddcbe6084b88f90da123ae1d29720104
    parent e32cfc51db7a4b4f2b7e9aa004433e360be665f8
    author amust <blurryface0906@gmail.com> 1770925146 +0300
    committer amust <blurryface0906@gmail.com> 1770925146 +0300
    gpgsig -----BEGIN SSH SIGNATURE-----
     U1NIU0lHAAAAAQAAADMAAAALc3NoLWVkMjU1MTkAAAAgtjkuMquAT2gzLwgzSIs6s62Glp
     8ct2xxpPAFuZcPdg4AAAADZ2l0AAAAAAAAAAZzaGE1MTIAAABTAAAAC3NzaC1lZDI1NTE5
     AAAAQGoXYLCraYmN7XhNZxg6JOtRT3tb/WbLsmy5PwrFPvI95ChzchER2qn7yApx5bK3tb
     J2qSrw02MhMfonREQmtQQ=
     -----END SSH SIGNATURE-----
    
    Add test file
```
**Пояснение:** Commit хранит "снимок" состояния проекта через ссылку на корневой tree, а также метаданные: родителя, автора, время и текст сообщения коммита. Именно commit связывает историю и конкретное состояние файлов.

```
    PS C:\Users\amust\DevOps-Intro> git cat-file -p 18991419ddcbe6084b88f90da123ae1d29720104
    040000 tree ccdeaa4f0a54135a5e884691d0db11d5af998de1    .github
    100644 blob db7abc0be45b7051f5e4d6c5bf99208a9ddf8e7a    README.md
    040000 tree a1061247fd38ef2a568735939f86af7b1000f83c    app
    040000 tree dd28a11f107ecd056848c2201f441149d2d47af9    images
    040000 tree 7a2e39b84db77786a7c83e1d6ef30baaffbacd1a    labs
    040000 tree d3fb3722b7a867a83efde73c57c49b5ab3e62c63    lectures
    100644 blob 418a98ced2ac70b5bdee0be9732ecdaae7264515    test.txt
```
**Пояснение:** Tree — это каталог, структура папки: список имён файлов/подпапок и ссылок на соответствующие объекты. Внутри tree каждая запись указывает либо на blob (файл), либо на другой tree (подкаталог), поэтому tree связывает имена в файловой структуре с содержимым.

```
    PS C:\Users\amust\DevOps-Intro> git cat-file -p 418a98ced2ac70b5bdee0be9732ecdaae7264515
    Test content
```

**Пояснение:** Blob — это содержимое файла как набор байт. blob не хранит имя файла и путь, эти данные хранятся в tree.

**Как Git хранит данные репозитория:**
Git хранит данные как набор объектов (blob, tree, commit), адресуемых по хэшу содержимого.
Commit ссылается на корневой tree, тем самым фиксируя "снимок" файлов и папок на момент коммита.
tree содержит записи вида "имя -> (blob/tree хэш)", поэтому через tree Git знает структуру каталогов и какие blob являются содержимым каких файлов.
blob содержит только контент файла, поэтому один и тот же blob может переиспользоваться, если содержимое файла не менялось.
В моём tree `18991419ddcbe6084b88f90da123ae1d29720104` есть запись `test.txt`, которая указывает на blob `418a98ced2ac70b5bdee0be9732ecdaae7264515`, и просмотр этого blob через `git cat-file -p` показывает строку `Test content`.


## Task 2 — Reset и Reflog (восстановление)
```
    PS C:\Users\amust\DevOps-Intro> git log --oneline -5
    90a40fc (HEAD -> git-reset-practice) Third commit
    d3f4932 Second commit
    5607830 First commit
    a8790d5 (feature/lab2) Add test file
    e32cfc5 (origin/main, origin/HEAD, main) Merge remote-tracking branch 'upstream/main
```

```
    PS C:\Users\amust\DevOps-Intro> git reset --soft HEAD~1
    PS C:\Users\amust\DevOps-Intro> git status
    On branch git-reset-practice
    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)
            modified:   file.txt
    
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
            .idea/
            labs/.idea/
            labs/submission2.md
    
    PS C:\Users\amust\DevOps-Intro> git log --oneline -5
    d3f4932 (HEAD -> git-reset-practice) Second commit
    5607830 First commit
    a8790d5 (feature/lab2) Add test file
    e32cfc5 (origin/main, origin/HEAD, main) Merge remote-tracking branch 'upstream/main
    6f044dd (upstream/main) Replace IPFS with Nix
```
**Пояснение:** После `git reset --soft HEAD~1` указатель `HEAD` (и ветка) сдвинулись на один коммит назад, то есть последний коммит исчез из видимой истории ветки (`git log` его больше не показывает). При этом индекс (staging area) сохранил изменения из “откаченного” коммита в статусе staged, а рабочая директория не откатывалась — файлы на диске остались с теми же правками. Это удобно, когда нужно переоформить последний коммит: изменить сообщение, добавить/убрать файлы и закоммитить заново.

```
    PS C:\Users\amust\DevOps-Intro> git reset --hard HEAD~1
    HEAD is now at 5607830 First commit
    PS C:\Users\amust\DevOps-Intro> git status
    On branch git-reset-practice
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
            .idea/
            labs/.idea/
            labs/submission2.md
    
    nothing added to commit but untracked files present (use "git add" to track)
    PS C:\Users\amust\DevOps-Intro> git log --oneline -5
    5607830 (HEAD -> git-reset-practice) First commit
    a8790d5 (feature/lab2) Add test file
    e32cfc5 (origin/main, origin/HEAD, main) Merge remote-tracking branch 'upstream/main
    6f044dd (upstream/main) Replace IPFS with Nix
    0a87e1c refactor: reduce prescriptiveness in GitLab CI instructions
```
**Пояснение:** Команда `git reset --hard HEAD~1` тоже двигает `HEAD` назад по истории, но дополнительно сбрасывает и индекс, и рабочую директорию под состояние выбранного коммита. В результате изменения, которые были в “откаченном” коммите, пропадают и из staged, и из файлов в рабочей папке (файлы становятся такими, как в целевом коммите). Этот режим используем только когда точно понимаем, что текущие незакоммиченные (лишние) правки не нужны.

```
    PS C:\Users\amust\DevOps-Intro> git reflog --date=iso
    5607830 (HEAD -> git-reset-practice) HEAD@{2026-02-12 23:11:20 +0300}: reset: moving to HEAD~1
    d3f4932 HEAD@{2026-02-12 23:07:06 +0300}: reset: moving to HEAD~1
    90a40fc HEAD@{2026-02-12 23:05:35 +0300}: commit: Third commit
    d3f4932 HEAD@{2026-02-12 23:05:23 +0300}: commit: Second commit
    5607830 (HEAD -> git-reset-practice) HEAD@{2026-02-12 23:05:06 +0300}: commit: First commit
    ...
```
```
    PS C:\Users\amust\DevOps-Intro> git reset --hard d3f4932
    HEAD is now at d3f4932 Second commit
    PS C:\Users\amust\DevOps-Intro> git log --oneline -5
    d3f4932 (HEAD -> git-reset-practice) Second commit
    5607830 First commit
    a8790d5 (feature/lab2) Add test file
    e32cfc5 (origin/main, origin/HEAD, main) Merge remote-tracking branch 'upstream/main
    6f044dd (upstream/main) Replace IPFS with Nix
```
**Пояснение:** После reset нужный коммит мог исчезнуть из обычного `git log`, потому что ветка перестала на него указывать, но Git всё равно помнит перемещения `HEAD` в журнале `reflog`. Я нашёл в `git reflog` запись с предыдущим состоянием `HEAD` (до reset) и взял оттуда хэш. Затем командой `git reset --hard <reflog_hash>` я вернул репозиторий в то состояние, и нужные коммиты/содержимое файлов снова появились.


## Task 3 — Граф истории (git log --graph)
```
    PS C:\Users\amust\DevOps-Intro> git log --oneline --graph --all
    * 446a8d4 (side-branch) Side branch commit
    * 0561da9 (HEAD -> feature/lab2) added task2
    | * d3f4932 (git-reset-practice) Second commit
    | * 5607830 First commit
    |/
    * a8790d5 Add test file
    *   e32cfc5 (origin/main, origin/HEAD, main) Merge remote-tracking branch 'upstream/main
    |\
    | * 6f044dd (upstream/main) Replace IPFS with Nix
    | * 0a87e1c refactor: reduce prescriptiveness in GitLab CI instructions
    | * eaea715 feat: add GitLab CI alternative instructions to lab3
    | | * 0fcb933 (origin/feature/lab1, feature/lab1) docs: complete lab1 report
    | | * 363679a docs: fixed submission1.md
    | | * 025718b docs: fixed PR template
    | | * 1abdd45 docs: add lab1 submission stub
    | |/
    |/|
    * | 77646db docs: fixed submission1.md
    * | d259eaf docs: add PR template and fixed submission1.md
    * | 157cc12 docs: add screenshots for signed commits
    * | d4a3172 docs: fixed image name
    * | fb0d796 docs: fixed image name
    * | 5baef11 docs: add commit signing summary for task1
    |/
    * d6b6a03 Update lab2
    * 87810a0 feat: remove old Exam Exemption Policy
    | * 0a09c16 (upstream/release/f25) feat: remove old Exam Exemption Policy
    |/
    * 1e1c32b feat: update structure
```
**Пояснение:** Вывод `git log --graph --all` помогает визуально понять структуру истории: где ветки расходились и где снова сходились, а также какие коммиты принадлежат каким веткам. Это снижает риск ошибиться при работе с несколькими ветками и облегчает отладку: видно, от какого коммита была создана ветка и какие изменения в ней появились. В моём графе видно, что side-branch содержит отдельный коммит Side branch commit, а ветка git-reset-practice живёт параллельно и не мешает ветке сдачи feature/lab2.

## Task 4 — Теги
```
    PS C:\Users\amust\DevOps-Intro> git tag v1.0.0
    PS C:\Users\amust\DevOps-Intro> git show --oneline --no-patch v1.0.0
    9ee414b (HEAD -> feature/lab2, tag: v1.0.0) added task3: history graph
    PS C:\Users\amust\DevOps-Intro> git push origin v1.0.0
    Enumerating objects: 13, done.
    Counting objects: 100% (13/13), done.
    Delta compression using up to 20 threads
    Compressing objects: 100% (10/10), done.
    Writing objects: 100% (11/11), 5.04 KiB | 645.00 KiB/s, done.
    Total 11 (delta 8), reused 0 (delta 0), pack-reused 0 (from 0)
    remote: Resolving deltas: 100% (8/8), completed with 2 local objects.
    To https://github.com/blurryface0906/DevOps-Intro.git
     * [new tag]         v1.0.0 -> v1.0.0
```
**Пояснение:** Теги — это именованные метки на конкретных коммитах, которые удобно использовать для версионирования: например, `v1.0.0` однозначно указывает на состояние проекта, которое мы считаем релизом. На платформах вроде GitHub релизы обычно строятся на основе Git-тегов, поэтому тег помогает публиковать релиз и привязывать к нему заметки/артефакты. Кроме того, в CI/CD теги часто используют как триггер (например, запуск пайплайна “собрать и задеплоить релиз” при появлении `v...`). В моём случае тег `v1.0.0` указывает на коммит `9ee414b` (это видно из `git show ...`)


## Task 5 — switch, checkout, restore
```
    PS C:\Users\amust\DevOps-Intro> git switch -c cmd-compare
    Switched to a new branch 'cmd-compare'
    PS C:\Users\amust\DevOps-Intro> git switch -
    Switched to branch 'feature/lab2'
    PS C:\Users\amust\DevOps-Intro> git switch cmd-compare
    Switched to branch 'cmd-compare'
    PS C:\Users\amust\DevOps-Intro> git status
    On branch cmd-compare
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
            .idea/
            labs/.idea/
    
    nothing added to commit but untracked files present (use "git add" to track)
    PS C:\Users\amust\DevOps-Intro> git branch
    * cmd-compare
      feature/lab1
      feature/lab2
      git-reset-practice
      main
      side-branch
```
**Пояснение:** `git switch` используется только для операций с ветками: создать ветку и перейти на неё (`git switch -c ...`), перейти на существующую ветку или быстро вернуться на предыдущую (`git switch -`). Это удобно, потому что команда не пытается восстанавливать файлы и не смешивает разные действия в одной команде.

```
    PS C:\Users\amust\DevOps-Intro> echo "scratch" >> demo.txt
    PS C:\Users\amust\DevOps-Intro> git status
    On branch cmd-compare
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
            .idea/
            demo.txt
            labs/.idea/
    
    nothing added to commit but untracked files present (use "git add" to track)
    PS C:\Users\amust\DevOps-Intro> git add demo.txt
    PS C:\Users\amust\DevOps-Intro> git status
    On branch cmd-compare
    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)
            new file:   demo.txt
    
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
            .idea/
            labs/.idea/
    
    PS C:\Users\amust\DevOps-Intro> git restore demo.txt
    PS C:\Users\amust\DevOps-Intro> git status
    On branch cmd-compare
    Changes to be committed:
      (use "git restore --staged <file>..." to unstage)
            new file:   demo.txt
    
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
            .idea/
            labs/.idea/
```
**Пояснение:** Когда `demo.txt` был untracked, Git не мог его восстановить, потому что файл ещё не существует в истории/индексе как отслеживаемый путь. Поэтому я дописал `git add demo.txt`, после чего файл стал известен Git (staged как `new file`), но `git restore demo.txt` по умолчанию восстанавливает содержимое из индекса в рабочую директорию, а у меня рабочая копия и так совпадала с тем, что было в индексе, поэтому `git status` остался прежним.

```
    error: pathspec 'demo.txt' did not match any file(s) known to git
    PS C:\Users\amust\DevOps-Intro> git add demo.txt
    PS C:\Users\amust\DevOps-Intro> git commit -m "chore: add demo.txt for restore demo"
    [cmd-compare 663bec4] chore: add demo.txt for restore demo
     1 file changed, 0 insertions(+), 0 deletions(-)
     create mode 100644 demo.txt
    PS C:\Users\amust\DevOps-Intro> echo "line after commit" >> demo.txt
    PS C:\Users\amust\DevOps-Intro> git status
    On branch cmd-compare
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
            modified:   demo.txt
            modified:   labs/submission2.md
    
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
            .idea/
            labs/.idea/
    
    no changes added to commit (use "git add" and/or "git commit -a")
    PS C:\Users\amust\DevOps-Intro> git restore --source=HEAD~1 demo.txt
    PS C:\Users\amust\DevOps-Intro> git status
    On branch cmd-compare
    Changes not staged for commit:
      (use "git add/rm <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
            deleted:    demo.txt
            modified:   labs/submission2.md
    
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
            .idea/
            labs/.idea/
    
    no changes added to commit (use "git add" and/or "git commit -a")
```
**Пояснение:** `git restore --source=HEAD~1 demo.txt` восстанавливает файл так, как он выглядел в коммите `HEAD~1`. Если в `HEAD~1` этого файла не было, Git приводит рабочую директорию к состоянию источника, поэтому файл помечается как удалённый (`deleted: demo.txt`). Это означает, что мы восстановили состояние прошлого коммита, где файл не существовал. Восстановление из конкретного коммита удобно для отката отдельных файлов без перемещения `HEAD` и без влияния на остальные изменения.

**Когда что использовать:** `git switch`, когда нужно безопасно и понятно работать с ветками (создать/переключиться/вернуться на предыдущую). `git restore` — когда нужно откатывать изменения в файлах или восстановить файл из конкретного коммита. `git checkout` исторически работает и с ветками, и с файлами, но из-за перегруженности его часто избегают практиках в пользу `switch` и `restore`.


## Challenges & Solutions
В пятом задании сначала не разобрался.Не добавил файл в гит и поэтому гит не мог с ним никак работать. Потом осознал, поправил, все заработало отлично.

Ещё в самом начале не мог выйти из vim xD. Мне помог мой сосед)

Я не успел на 20 минут(

## GitHub Community
**Почему важны звёзды:** Звезды в GitHub помогают помечать полезные репозитории и быстро находить их позже (как закладки), а также помогают в поиске релевантных проектов (GitHub может рекомендовать похожие репозитории). Кроме того, звёзды — это простой способ поддержки и интереса к проекту со стороны сообщества, который влияет на видимость репозитория.

**Почему важны подписки:** Подписки на других членов сообщества помогают видеть их активность (новые репозитории, PR, обсуждения) и быстрее учиться, обмениваться опытом. В командной работе это упрощает коммуникацию и поиск нужных людей, потому что легче следить и видеть, кто над чем работает.
