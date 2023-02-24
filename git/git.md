# Git 명령어 및 사용법

## `git mv` - 파일이나 폴더 이름 변경, 디렉토리 변경

1. 로컬 환경에서의 이름 변경 작업은 git에 반영되지 않는다.
   대신 [git mv 명령어로 이름을 바꿀 수 있다.](https://docs.github.com/en/repositories/working-with-files/managing-files/renaming-a-file#renaming-a-file-using-the-command-line)

```
git mv <기존이름> <새로운이름>
```

<br>

2. git mv 명령어로 파일을 다른 폴더로 옮길 수 있다. ([git docs](https://git-scm.com/docs/git-mv#:~:text=In%20the%20second%20form%2C%20the%20last%20argument%20has%20to%20be%20an%20existing%20directory%3B%20the%20given%20sources%20will%20be%20moved%20into%20this%20directory.) 참고)

- 이 때, 옮길 파일의 현재 경로와 cmd 경로가 동일해야한다.

<br>

3. 대소문자만 변경하는 법

- [StackOverflow - Changing capitalization of filenames in Git](https://stackoverflow.com/questions/10523849/changing-capitalization-of-filenames-in-git)

<br>

## git 커밋 메시지 수정 또는 삭제

"[Changing a commit message](https://docs.github.com/en/pull-requests/committing-changes-to-your-project/creating-and-editing-commits/changing-a-commit-message)" 참고

### 직전 커밋 수정

1. `git commit --amend` 명령어를 통해 커밋 메시지를 수정할 수 있다.  
   (push 이전일 때는 여기까지만 하면 된다.)

2. 강제로 push해서 기존 커밋을 덮는다.
   ```shell
   git push --force-with-lease 원격저장소명 브랜치명
   ```

### 오래된 커밋 수정 & 여러 커밋 한 번에 수정

1. 직전 커밋부터 시작해서 커밋 n개를 불러온다.

   ```bash
   $ git rebase -i HEAD~n
   ```

   그러면 아래와 같은 창이 뜨는데, 수정할 커밋 메시지 앞에 쓰인 `pick`을 `reword`로 바꾼다.

   ```
   pick 6b4e2f5 :memo: markdown.md 내용 수정
   pick 8d103d2 :memo: README.md에 문서 링크 추가
   pick a4a925c :memo: copyright.md 내용 수정
   pick f0fcd93 :memo: dependency.md 내용 수정

   # Rebase 0ed3c6f..f0fcd93 onto 0ed3c6f (4 commands)
   #
   # Commands:
   # p, pick <commit> = use commit
   # r, reword <commit> = use commit, but edit the commit message
   # e, edit <commit> = use commit, but stop for amending
   # s, squash <commit> = use commit, but meld into previous commit
   # f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
   #                    commit's log message, unless -C is used, in which case
   #                    keep only this commit's message; -c is same as -C but
   #                    opens the editor
   # x, exec <command> = run command (the rest of the line) using shell
   # b, break = stop here (continue rebase later with 'git rebase --continue')
   # d, drop <commit> = remove commit
   # l, label <label> = label current HEAD with a name
   # t, reset <label> = reset HEAD to a label
   # m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
   # .       create a merge commit using the original merge commit's
   # .       message (or the oneline, if no original merge commit was
   # .       specified); use -c <commit> to reword the commit message
   #
   # These lines can be re-ordered; they are executed from top to bottom.
   #
   # If you remove a line here THAT COMMIT WILL BE LOST.
   #
   # However, if you remove everything, the rebase will be aborted.
   #
   ```

2. 커밋 메시지 수정 후 git에 강제로 push한다.
   ```bash
   $ git push --force 원격저장소명 브랜치명
   ```

### 커밋 삭제

참고 자료

- owljoa. "[Rebase 활용한 특정 커밋 수정/제거.](https://velog.io/@owljoa/Rebase-%ED%99%9C%EC%9A%A9%ED%95%9C-%ED%8A%B9%EC%A0%95-%EC%BB%A4%EB%B0%8B-%EC%88%98%EC%A0%95%EC%A0%9C%EA%B1%B0)"
-
