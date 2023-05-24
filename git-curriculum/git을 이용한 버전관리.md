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

#### git branch

#### git stash

#### git reset

#### git diff

#### git revert

#### git rebase



# 4. 실무에 쓰이는 깃 협업 시나리오

> 로컬과 원격 상호작용



> 로컬과 원격 충돌이 난 경우



> 로컬은 변함없는데 원격이 변한 경우



> 로컬도 변했는데 원격도 변한 경우



# 5. 그 외 문제 해결

> 1. 이미지를 마크다운(.md파일)에 추가하기 위해서는 해당 이미지를 깃허브에 파일 업로드를 같이 해줘야한다.