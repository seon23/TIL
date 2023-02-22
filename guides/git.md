## `git mv` 폴더 이름 변경 또는 이동

로컬 환경에서의 이름 변경 작업은 git에 반영되지 않는다.
대신 [git mv 명령어로 이름을 바꿀 수 있다.](https://docs.github.com/en/repositories/working-with-files/managing-files/renaming-a-file#renaming-a-file-using-the-command-line)

```
git mv <기존이름> <새로운이름>
```

<br>

git mv 명령어로 파일을 다른 폴더로 옮길 수 있다. ([git docs](https://git-scm.com/docs/git-mv#:~:text=In%20the%20second%20form%2C%20the%20last%20argument%20has%20to%20be%20an%20existing%20directory%3B%20the%20given%20sources%20will%20be%20moved%20into%20this%20directory.) 참고)

- 이 때, 옮길 파일의 현재 경로와 cmd 경로가 동일해야한다.

### 대소문자만 변경하는 법

[StackOverflow - Changing capitalization of filenames in Git](https://stackoverflow.com/questions/10523849/changing-capitalization-of-filenames-in-git)
