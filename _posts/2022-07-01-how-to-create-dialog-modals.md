---
layout: post
title:  "비전공자에게 내새끼(나의 코딩) 설명하기 시리즈 02"
---

# 💡 비전공자에게 내새끼(나의 코딩) 설명하기 시리즈 2️⃣ - 확인창

## 사용자를 안심시키는 UX, 확인 모달창

### 1. 모달창이 뭐야?

<img width="374" alt="dialog_confirm_revoke" src="https://user-images.githubusercontent.com/79065544/176932336-6ef26a3a-d6c3-4c8f-aa25-fedd740487c1.png">
<img width="386" alt="dialog_login_required" src="https://user-images.githubusercontent.com/79065544/176932344-f8f4b12a-1f5e-4a6c-b939-48de752e989a.png">
<img width="877" alt="dialog_shopping_cart" src="https://user-images.githubusercontent.com/79065544/176932446-e4866708-0d5b-4488-b77f-409078557426.png">


로그인을 하려고 할 때 보고 있던 화면이 조금 불투명해 지면서 그 위에 팝업창 같은 게 뜨는 경우를 본 적 있을 거야. 페이지의 주소는 그대로 있으니 다른 페이지로 이동한 것은 아니고 내가 보고 있던 페이지 위에 하나 또는 여러 개의 레이어가 덧대어지는 건데 그것을 모달이라고 불러. 아니면 쇼핑몰에서 장바구니 버튼을 누르면 페이지는 바뀌지 않았는데 현재 담겨진 품목들이 오른쪽에 주르륵 보이는 경우가 바로 모달창이라는 거야.

페이지를 이동하는 게 나쁘지는 않지. 오히려 화면이 전환되면서 사용자가 다음의 행위에 집중할 수 있으니까. 하지만 전의 화면을 다시 보기 위해서는 뒤로 가기를 눌러야하는 번거로움이 있잖아? 그런데 모달창은 팝업된 부분의 바깥을 클릭하거나 모달 안에 들어있는 버튼을 누르면 바로 그 전의 페이지를 볼 수 있다는 편리함 때문에 웹 서비스에서 널리 쓰이고 있어.  

### 2. 모달창의 구성
모달은 적어도 세 개의 구성요소가 필요해. 백드랍이라고 불리는 배경 레이어, 그 위에 보여질 팝업 레이어, 그리고 그 팝업 안의 컨텐츠야. 백드랍이 없는 경우도 보긴 했는데 팝업창에 집중할 수 있으려면 뒤는 흐림 처리를 해주는 것이 좋다고 생각하는 편이야.

1. 레이어
  지금 레이어는 두 개가 있지? 백드랍과 팝업. 한 겹의 화면이 현재 보는 화면의 위에 얹어진다는 건데 이건 보통 CSS(Cascading Style Sheets)에서 구현할 수 있어. CSS는 눈 아프게 텍스트만 있는 HTML을 보기 좋게 꾸며주는 언어인데 그 중에 `z-index`라는 값을 높여 주면 레이어 위에 레이어가 쌓이게 돼. 좌표축에는 x, y, z축이 있잖아? x와 y는 가로와 세로의 2차원 좌표축이고 z축은 앞뒤로 이동하기 때문에 3차원의 좌표축인거지. `z-index`의 기본값은 0이고 양수가 되면 앞으로 튀어나오고 음수가 되면 뒤로 배치되는 거야. 
2. 컨텐츠
  컨텐츠는 팝업 레이어와 같은 레이어에 보여지는 내용이야. 어떤 구성요소가 있을 지는 상황에 따라 매우 다양하지. 그냥 안내 메세지만 있을 수도 있고 그 메세지와 함께 확인이나 이동 버튼이 있을 수도 있고 아니면 거의 페이지에 해당할 정도로 많은 요소가 들어가 있으면서 클릭하면 그 위에 또 쌓이고 또 쌓이고 할 수도 있는 거지. 내 프로젝트의 경우에는 안내 메세지, 확인과 닫음 버튼으로 구성되어 있어. 꼭 엑스를 누르지 않고 주변의 백드랍을 눌러도 모달이 닫히기는 하는데 그냥 밸런스가 예뻐서 넣어 놨어 😄
  

### 3. 모달의 상태 - 열렸게, 닫혔게?
컴퓨터는 알잘딱깔센 과는 아니지. 내가 닫힘 버튼을 눌렀으면 알아서 팝업도 닫고 백드랍도 없애고 해야하는데 하나하나 명령을 잘 해놓지 않으면 팝업은 사라졌는데 백드랍만 덜렁 남아있거나 페이지를 이동했는데 아까 떴던 팝업이 계속 열려있을 수도 있단 말야. 그래서 전역에서 모달의 열림/닫힘 상태를 관리해 줘야해. 

나는 `Redux Toolkit`이라는 라이브러리를 사용해서 모달의 상태를 관리했어. 
```js
import { createSlice } from "@reduxjs/toolkit";

const initialState = { // 코드를 보니 머리아프지? 여기 상태 3줄만 봐보자.
  isOpen: false,
  componentName: null,
  childrenProps: {},
};

const modalSlice = createSlice({
  name: "modal",
  initialState,
  reducers: {
    openModal: (state, action) => {
      state.isOpen = true;
      state.componentName = action.payload.name;
      state.childrenProps = action.payload.childrenProps;
    },
    closeModal: (state) => {
      state.isOpen = false;
      state.componentName = null;
      state.childrenProps = {};
    },
  },
});

...
```
- `isOpen`이라는 상태를 만들어 놓고 참/거짓으로 변경할 수 있어. 
- `componentName`는 어떤 모달인지, 예를 들어 로그인 페이지로 이동하라는 모달창인지 아니면 게시물을 정말 삭제할 거냐고 경고하는 모달창인지를 명시해주는 거지. 디폴트는 모달이 안 열렸다는 거고, 모달이 '없다'는 거니까 당연히 모달의 이름도 없겠지?
- `childrenProps`는 빈 객체인데 이 안에 필요한 속성을 물려주기도 하고 안 물려주기도 해. 예를 들어 탈퇴를 한다고 하면 어떤 사용자가 탈퇴하겠다는 건지 id를 콕 찝어서 알려줘야 그걸 백엔드에서 찾아서 알맞은 사용자의 데이터를 삭제하겠지? 전에 댓글 포스팅에서 모든 댓글은 고유의 아이디가 있다고 했듯이 사용자도 게시글도 모두 아이디가 있거든. 만약 서비스를 이용하기 전에 로그인을 해야한다는 안내 메세지만 띄우고 서버에 딱히 요청을 보낼 것이 없다면 자식 속성을 굳이 물려주지 않아도 돼.


### 4. 모달 렌더링하기(만들기)
다양한 형태의 모달이 있다보니 그 갯수만큼 컴포넌트의 수도 여러 개야. 컴포넌트는 웹페이지를 구성하는 여러 요소를 작은 단위로 쪼개어 둔 것인데 이렇게 쪼개어 두면 에러가 어떤 컴포넌트에서 난 건지 들여다보기도 쉽고 변경사항을 적용하기도 간편하지. 나도 모달 컴포넌트의 기본 틀을 하나 만들어 놓고 그 안에 내용과 버튼의 기능만 부분적으로 변경해서 다양한 모달로 재사용했어.

위에서 모달의 상태를 관리하는 `Redux Toolkit` 라이브러리를 사용했다고 했지? 그 안에는 `useSelector`라는 후크(상태에 연결해주는 기능)가 있어. 그걸 사용해서 `{ isOpen, componentName, childrenProps }` 이 세 가지 상태에 후크를 걸어 끌어오는 거야. 아래 초록 코멘트만 봐보자.

```js
...
  const { isOpen, componentName, childrenProps } = useSelector((state) => state.modal);

  const componentsLookUp = { // 컴포넌트 이름이 담긴 객체
    UpdateUser,
    DeleteUser,
    UpdateStudy,
    DeleteStudy,
    WriteStudy,
    NoResults,
    DirectToLogin,
    EmailVerification,
  };

  let renderComponent;
  if (componentName) { // 내가 열려는 모달이 componentsLookUp에 존재한다면? 
    const SelectedComponent = componentsLookUp[componentName]; // 그걸 선택된 컴포넌트라고 해준다.
    if (SelectedComponent) { // 선택된 컴포넌트가 존재하니까 자식 속성을 물려받아 렌더링을 한다.
      renderComponent = <SelectedComponent {...childrenProps} />;
    }
  }
  ...
```

이렇게 해서 렌더링이 잘 끝나면 알맞은 확인창이 열리고 닫히고 하는 거지. 확인창에 어떤 프로세스가 숨어 있는지 대략적으로 설명을 해봤는데 도움이 되었길 바라! 

> 다음 이야기

로그인 하나 하는데 서버 백조가 이렇게 힘들게 물장구를 치고 있는지 배우기 전엔 몰랐어... 비밀 키를 주고 받는 인증 과정을 알아보자!
