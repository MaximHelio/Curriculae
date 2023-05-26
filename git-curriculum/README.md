# 0. INSTALL

[[GITHUB 입문\] Git 설치하기(2.35.1 이상, 상세한 설치법) (tistory.com)](https://taewow.tistory.com/13) 참고

# 1. 버전관리 개념

> 하나의 버전이 관리되는 과정에서 효율적인 백업이 필요하다

### 버전이 되기까지 거쳐가는 세 개의 공간

![image-20230525001110734](./그림1.png)

#### 

# 2. github 회원가입 및 github 메인 꾸미기

> 다른 사람들 git을 보고, 메인 화면 등을 꾸며보며
> git add, git commit, git push 작업에 익숙해지기

```bash
// 0. 원격과 싱크 맞추기
git pull origin

```로컬에서 변경 작업```

// 1. 수정된 파일들 상태 보기
git status

// 2. 수정된 파일들 한꺼번에 올릴 때(.대신 파일명을 써서 하나씩 올릴 수도 있다)
git add .

// 3. Working area(작업공간)에서 staging area에 올리기 
git commit -m "수정 메모"

// 4. 원격에 반영: git push 저장소명 브랜치명
git push origin main
```



# 3. 여러가지 git 개념

```bash
git branch
```

```bash
git stash
```

```bash
git reset
```

```bash
git diff
```

```bash
git revert
```

```bash
git rebase
```

# 4. 실무에 쓰이는 깃 협업 시나리오

> 로컬과 원격 상호작용



> 로컬과 원격 충돌이 난 경우



> 로컬은 변함없는데 원격이 변한 경우



> 로컬도 변했는데 원격도 변한 경우



# 5. 그 외 팁

```
이미지를 마크다운(.md파일)에 추가하기 위해서는 해당 이미지를 깃허브에 파일 업로드를 같이 해줘야한다.
```

```python
README파일? 

- 깃(Git) 자체적으로는 README 파일을 최상단에 위치한 마크다운 파일로 자동으로 인식하거나 특별한 처리를 하는 기능을 가지고 있지는 않는다. 

- 하지만 깃 호스팅 플랫폼(예: GitHub, GitLab, Bitbucket)은 프로젝트 관리를 위해 일반적으로 README 파일을 프로젝트의 설명 및 문서화를 위한 핵심 파일로 인식하며, 프로젝트 페이지에서 README 파일의 내용을 표시하여 사용자들에게 프로젝트에 대한 정보를 제공한다. 
- 따라서, README 파일을 최상단에 위치한 마크다운 파일로 작성하면, 해당 파일의 내용이 프로젝트 페이지에서 표시되는 것이다. 
```

```
git은 해당 폴더를 추적하고 있으므로, 폴더 밖 작업(이름 수정 등)이 안된다. 
```

