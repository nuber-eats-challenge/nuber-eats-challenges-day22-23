# nuber-eats-challenges-day22-23

<details>
  <summary>
  Day 19 정답해설
  </summary>

  1. 일단 소스를 다운 받아서 압축을 풀어주도록 합니다.
  2. 소스를 받고 생각해보실 문제가, heroku에서는 sqlite3를 사용할 수가 없다는 것입니다. 왜냐면 저장공간이 정적이라 주기적으로 파일을 청소하기 때문에 sqlite3 안에 파일이 주기적으로 날라갑니다. 그래서 다른 db를 사용해야 하는데, 강의에서는 사용한 postgres를 사용하도록 합니다.
  - postgres를 사용하기 위해서는 pg 패키지를 설치해주셔야 합니다.
  - ```npm i pg```
  3. deploy 방법에는 두 가지가 있습니다.
  - heroku cli를 이용하는 방법.
  - heroku 홈페이지를 이용하는 방법: 솔루션에서는 heroku cli를 이용 합니다. heroku cli를 설치하도록 합니다. 설치는 OS마다 방법이 다르므로 위에 링크를 참고하여 설치하도록 합니다.
  4. 일단 우리한테 필요한 것은 git repository에 백엔드 소스 코드와 연결해주는 것입니다. 본인의 git 계정으로 repository를 만들고 백엔드 소스와 연결을 해줍니다.
  5. GraphQL과 Postgres 설정을 해주도록 합니다. GraphQL은 크게 설정할 것이 없습니다.
  - GraphQL 설정: NODEV_ENV=production모드, 즉 배포모드에서는 playground가 기본적으로 false입니다. playground: true 옵션을 주셔야 하고, introspection: true 옵션도 주도록 합니다.  
    ![](https://i.ibb.co/fNZjJs8/graphql.png)
  - TypeORM 설정
    ![](https://i.ibb.co/BZm5pws/typeorm-1.png)
  
  - typeorm설정은 nuber-eats 설정을 따랐습니다. conditional spread를 이용하여 production이냐 아니냐, DATABASE_URL이 환경변수로 제공되냐 안되냐에 따라 설정을 달리했습니다.
  - DATABASE_URL에는 postgres://[id]:[password]@[host]:[port]/[database]의 형태로 정보를 넘겨줘서 한 줄에 정보를 다 받을 수 있는데, heroku의 postgres를 이용하면 자동으로 DATABSE_URL을 설정을 해줍니다.
  - ssl 옵션이 보일 것인데, 사실 deploy할 때 골치 아픈 부분 중 하나입니다.
  - [#24.2 Heroku conclusion](https://nomadcoders.co/nuber-eats/lectures/2305)의 댓글을 참조하시면, 도움이 될 것입니다.
  6. Heroku app & postgres db 만들기
  - 더 진행하기 전에 heroku app을 만들도록 합니다. 일단 app을 만들고 그 다음 db 만드는 과정을 이어 나가도록 합니다.
  - 먼저 heroku login을 하도록 합니다. 브라우저에 로그인 과정을 거치면 heroku cli에도 연동이 되어 로그인 됩니다.
  - 다운 받은 소스 코드의 폴더로 이동하여 heroku app을 만듭니다.
  - ```terminal) heroku create```
    ![](https://i.ibb.co/0n2Cn6c/2021-04-19-10-34-20.png)
    ![](https://i.ibb.co/RvrvfZY/2021-04-19-10-36-44.png)

  - 먼저 위 그림처럼 Resource 탭에서 Find more add-ons 버튼을 누르도록 합니다.
    ![](https://i.ibb.co/PQjktF5/2021-04-19-10-41-29.png)

  - 여러가지 app 중에 heroku postgres를 누르셔서 설치하도록 합니다. Add-on plan에 HobbyDev-Free를 누르시고 우리가 만든 앱과 연결하도록 합니다. 놀랍게도 db 설정이 끝났습니다. 자동으로 연결한 앱에 DATABASE_URL 설정을 해줬습니다.
  - 앱의 Settings > Config Vars > Reveal Config Vars를 보시면 DATABASE_URL이 들어가 있는 것을 확인할 수 있습니다.
  7. Procfile 만들기
  - Procfile은 app 종류와 시작할 때 어떤 명령을 내릴지 정의해 놓은 파일입니다.
  - ```web: npm run start:prod```
  - Procfile은 이렇게 만들어 놓으면 됩니다. 그러면 heroku에서는 "이 친구는 web 앱이고, npm run start:prod 명령을 내리면 앱이 시작되는거야."로 이해할 수 있게 됩니다.
  - Procfile은 프로젝트의 루트 폴더에 만들어 주시면 됩니다.
  8. PORT 설정하기
  - heroku는 기본적으로 PORT를 제공하며, 그 PORT로 서버를 열도록 해줘야 합니다.
    ![](https://i.ibb.co/2stKyyc/main-1.png)
  - 위 그림은 main.ts 입니다. ```await app.listen(3000);```을 ```await app.listen(process.env.PORT||3000);```으로 바꿔줍니다.
  9. deploy to heroku
  - heroku cli에서는 git을 이용하여 deploy하기 때문에 일단 변경사항은 모두 commit해 두도록 합니다.
  - ```terminal) git remote``` 해보시면 자동으로 heroku 브랜치가 만들어지신게 보이시나요? 여기서 git push를 하면 자동으로 heroku에서 우리의 코드를 올리고 의존성 패키지들을 설치하고, typescript도 컴파일하고 서버를 시작하게 됩니다.
  - ```terminal) git push heroku master```

  ###결론
  - heroku는 무료에다가 https를 사용할 수 있기 때문에 매우 유용합니다. 에러가 나면 골치 아프시지만, 에러메시지와 로그를 잘 활용하셔서 꼭 성공하시길 바랍니다.
  - 백엔드가 끝났습니다. 저번 챌린지 결론에서 말씀드린대로 완전히 끝난 것은 아닙니다. 열심히 복습하셔서 챌린지 끝까지 좋은 결과 이루시길 바랍니다.
</details>

### Welcome to Frontend!!

- 오늘의 강의: 우버 이츠 클론코딩 강의 #14.3 - #15.9 (21.09.13 - 14)
- 오늘의 과제: 위의 강의를 시청하신 후, 아래 코드 챌린지를 제출하세요.

### Code Challenge
- 프론트 엔드 파트 시작입니다.
- 챌린지 기간은 2일입니다.
- 이번 챌린지 과제에서는 전에 만든 백엔드 API를 이용하여 로그인을 구현하도록 합니다.
- [ ]  Router 설정(react-router-dom을 이용하여)
- [ ]  apollo-client 설정
- [ ]  react-hooks-form을 이용하여 로그인 스크린과 Form을 만듭니다.
- [ ]  login mutation을 만듭니다(useMutation).
- [ ]  로그인 token을 저장하도록 합니다
- 로그인 한 유저를 보여주는 페이지는 reactive variables를 이용합니다.
- 샌드박스의 보일러플레이트에는 TailwindCSS 가 링크되어 있습니다. UI 디자인은 너무 신경 안쓰셔도 됩니다. 이 후 챌린지 과제로 나옵니다.

<details>
  <summary>
  Hint
  </summary>

  - 강의에 나온대로 router를 로그인 한 경우, 로그인 하지 않은 경우로 나눠서 생각하시면 쉽습니다.
  - makeVar, useReactiveVar를 사용합니다.
  - apollo codegen이 기억 나시나요? 안타깝게 코드샌드박스에서는 apollo codegen을 사용할 수가 없습니다. 방법은 두 가지가 있습니다.
  - 방법1: useMutation, useQuery 등의 함수에 type 제공 없이 사용할 수 있도록 설정되어 있습니다.
  - 방법2: 코드샌드박스 코드를 다운 받아서 진행한다.(후기의 [#Assignment 12](https://nomadcoders.co/community/thread/294) 참고)
  - 토큰은 localStorage에 저장하는 방법을 이용합니다.
</details>