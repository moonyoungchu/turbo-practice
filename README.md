
# 모노레포 STUDY 필기

# 레포 구조
package.json
- 작업공간의 기반

root turbo.json
- 작업구성 

# 자주 빠지는 함정
> **TypeScript를 사용하고 있다면, 워크스페이스 루트에 `tsconfig.json`을 둘 필요는 거의 없습니다.**
> 각 패키지는 독립적으로 자신만의 설정을 지정해야 하며, 보통 워크스페이스 안의 별도 패키지에 정의된 \*\*공유 `tsconfig.json`\*\*을 기반으로 확장하는 방식을 사용합니다. 자세한 내용은 TypeScript 가이드를 참고하세요.
>
> **패키지 경계를 넘어 파일에 직접 접근하는 것은 최대한 피하는 것이 좋습니다.**
> 만약 다른 패키지로 가기 위해 `../` 같은 상대 경로를 쓰고 있다면, 그건 아마도 접근 방식을 다시 생각해볼 기회일 수 있습니다. 필요한 위치에 해당 패키지를 설치하고, 코드 안에서 import 해서 사용하는 것이 올바른 방법입니다.

👉 요약하면:

* 루트 `tsconfig.json` → 거의 필요 없음
* 각 패키지는 독립적인 `tsconfig.json`을 가지되, 공통 설정은 `packages/typescript-config` 같은 곳에서 불러와 확장
* `../`로 다른 패키지 접근 ❌ → 워크스페이스 패키지로 import 해서 사용해야 함


# 종속성 관리

저장소에 종속성을 설치할 때는 해당 종속성을 사용하는 패키지에 직접 설치해야 합니다. 

🔑 원칙
루트 package.json에는 실행 환경 전체에서 공용으로 사용하는 개발 도구(devDependencies)만 두는 것이 바람직함.
실제 실행되는 애플리케이션이나 라이브러리에 필요한 의존성은 반드시 해당 애플리케이션 또는 패키지의 package.json에 포함시켜야 함.



# 내부 패키지 생성
내부 패키지 는 작업 공간의 기본 요소로, 리포지토리 전체에서 코드와 기능을 공유할 수 있는 강력한 방법을 제공합니다. 

내부 패키지를 만들 때는 단일 "목적"을 가진 패키지를 만드는 것이 좋습니다. 이는 엄격한 과학이나 규칙은 아니지만, 저장소, 규모, 조직, 팀의 요구 사항 등에 따라 달라지는 모범 사례입니다. 이 전략에는 여러 가지 장점이 있습니다.

이해하기 쉬움 : 저장소가 확장되면 저장소에서 작업하는 개발자는 필요한 코드를 더 쉽게 찾을 수 있습니다.
패키지당 종속성 줄이기 : 패키지당 종속성을 줄이면 Turborepo가 ​​패키지 그래프의 종속성을 보다 효과적으로 제거 할 수 있습니다 .
다음은 몇 가지 예입니다.

- @repo/ui: 공유 UI 구성 요소를 모두 포함하는 패키지
- @repo/tool-specific-config: 특정 도구의 구성을 관리하기 위한 패키지
- @repo/graphs: 그래픽 데이터를 생성하고 조작하기 위한 도메인별 라이브러리


# 작업 구성
루트 turbo.json파일은 Turborepo가 ​​실행할 작업을 등록하는 곳입니다.

# 실행중인 작업
turborepo는 작업을 자동으로 병렬화하고 캐싱하여 저장소의 개발자 워크플로를 최적화합니다. 작업이 turbo.json 에 등록 되면 저장소에서 스크립트를 실행할 수 있는 강력한 새 도구 세트를 사용할 수 있습니다.


자동 패키지 범위 지정
패키지 디렉터리에 있을 때 turbo는 해당 패키지의 패키지 그래프로 명령 범위를 자동 설정합니다 . 즉, 패키지에 대한 필터를 작성 하지 않고도 명령을 빠르게 작성할 수 있습니다.

*알아두면 좋은 정보: 필터를 사용하면 자동 패키지 범위 지정이 무시됩니다.

커스터마이징 동작
- 가장 자주 사용하는 명령어의 변형 : 현재 특정 패키지에만 관심이 있을 수도 있습니다. 사용하려는 특정 패키지를 빠르게 필터링할 수 있습니다 turbo build --filter=@repo/ui.
- 일회성 명령 : turbo build --dry package.json 스크립트를 따로 만들지 않아도 됩니다. 필요할 때마다 터미널에서 직접 실행할 수 있습니다.


### 1. 특정 패키지에만 관심 있는 경우 → `--filter`
모노레포에서는 수십 개 패키지가 있는데, 지금은 UI 컴포넌트만 수정했을 때 전체를 빌드할 필요는 없음.
이때 `--filter` 옵션으로 원하는 패키지에만 실행 범위를 좁힐 수 있음.

예시

```bash
# @repo/ui 패키지만 빌드
turbo build --filter=@repo/ui
```

* 실제로는 `apps/web`, `apps/mobile`, `packages/ui`, `packages/utils` 같은 구조라면,
  `packages/ui`만 빌드하게 됨.
* CI/CD에서도 바뀐 패키지와 그 의존 패키지만 실행할 수 있어서 속도 이득이 큼.

👉 조금 더 응용하면:

```bash
# ui 패키지와 그걸 의존하는 앱까지 같이 빌드
turbo build --filter=@repo/ui...
```

(뒤에 `...` 붙이면 `ui`를 사용하는 다른 패키지들도 자동 포함)

### 2. 일회성 명령 실행 → `--dry-run`

터보레포는 `turbo.json`이나 `package.json`에 스크립트를 정의해놓고 돌리지만,
가끔은 “이 명령 실행하면 뭐가 돌지?” 미리 확인하고 싶을 때가 있음.
그럴 때 `--dry-run`을 쓰면 실제 실행은 안 하고 **실행될 작업 목록**만 출력해줌.

예시

```bash
# 어떤 빌드가 실행될지 확인만 하기
turbo build --dry-run
```

출력 예시:

```
Tasks to run:
- packages/ui#build
- apps/web#build
- apps/mobile#build
```

👉 활용 포인트:
* 새로운 필터 조합이 잘 먹히는지 확인할 때
* 캐시 덕분에 실제로는 몇 개만 실행되는지 확인할 때
* CI에서 “빌드 영향 범위 체크” 용도로 활용 가능


### 정리
* **`--filter`** : 관심 있는 특정 패키지(또는 그와 관련된 패키지)만 실행 → 빠르고 효율적.
* **`--dry-run`** : 실행 없이 어떤 작업이 돌지 확인 → 안전하게 확인 가능.





----
# Turborepo starter

This Turborepo starter is maintained by the Turborepo core team.

## Using this example

Run the following command:

```sh
npx create-turbo@latest
```

## What's inside?

This Turborepo includes the following packages/apps:

### Apps and Packages

- `docs`: a [Next.js](https://nextjs.org/) app
- `web`: another [Next.js](https://nextjs.org/) app
- `@repo/ui`: a stub React component library shared by both `web` and `docs` applications
- `@repo/eslint-config`: `eslint` configurations (includes `eslint-config-next` and `eslint-config-prettier`)
- `@repo/typescript-config`: `tsconfig.json`s used throughout the monorepo

Each package/app is 100% [TypeScript](https://www.typescriptlang.org/).

### Utilities

This Turborepo has some additional tools already setup for you:

- [TypeScript](https://www.typescriptlang.org/) for static type checking
- [ESLint](https://eslint.org/) for code linting
- [Prettier](https://prettier.io) for code formatting

### Build

To build all apps and packages, run the following command:

```
cd my-turborepo

# With [global `turbo`](https://turborepo.com/docs/getting-started/installation#global-installation) installed (recommended)
turbo build

# Without [global `turbo`](https://turborepo.com/docs/getting-started/installation#global-installation), use your package manager
npx turbo build
yarn dlx turbo build
pnpm exec turbo build
```

You can build a specific package by using a [filter](https://turborepo.com/docs/crafting-your-repository/running-tasks#using-filters):

```
# With [global `turbo`](https://turborepo.com/docs/getting-started/installation#global-installation) installed (recommended)
turbo build --filter=docs

# Without [global `turbo`](https://turborepo.com/docs/getting-started/installation#global-installation), use your package manager
npx turbo build --filter=docs
yarn exec turbo build --filter=docs
pnpm exec turbo build --filter=docs
```

### Develop

To develop all apps and packages, run the following command:

```
cd my-turborepo

# With [global `turbo`](https://turborepo.com/docs/getting-started/installation#global-installation) installed (recommended)
turbo dev

# Without [global `turbo`](https://turborepo.com/docs/getting-started/installation#global-installation), use your package manager
npx turbo dev
yarn exec turbo dev
pnpm exec turbo dev
```

You can develop a specific package by using a [filter](https://turborepo.com/docs/crafting-your-repository/running-tasks#using-filters):

```
# With [global `turbo`](https://turborepo.com/docs/getting-started/installation#global-installation) installed (recommended)
turbo dev --filter=web

# Without [global `turbo`](https://turborepo.com/docs/getting-started/installation#global-installation), use your package manager
npx turbo dev --filter=web
yarn exec turbo dev --filter=web
pnpm exec turbo dev --filter=web
```

### Remote Caching

> [!TIP]
> Vercel Remote Cache is free for all plans. Get started today at [vercel.com](https://vercel.com/signup?/signup?utm_source=remote-cache-sdk&utm_campaign=free_remote_cache).

Turborepo can use a technique known as [Remote Caching](https://turborepo.com/docs/core-concepts/remote-caching) to share cache artifacts across machines, enabling you to share build caches with your team and CI/CD pipelines.

By default, Turborepo will cache locally. To enable Remote Caching you will need an account with Vercel. If you don't have an account you can [create one](https://vercel.com/signup?utm_source=turborepo-examples), then enter the following commands:

```
cd my-turborepo

# With [global `turbo`](https://turborepo.com/docs/getting-started/installation#global-installation) installed (recommended)
turbo login

# Without [global `turbo`](https://turborepo.com/docs/getting-started/installation#global-installation), use your package manager
npx turbo login
yarn exec turbo login
pnpm exec turbo login
```

This will authenticate the Turborepo CLI with your [Vercel account](https://vercel.com/docs/concepts/personal-accounts/overview).

Next, you can link your Turborepo to your Remote Cache by running the following command from the root of your Turborepo:

```
# With [global `turbo`](https://turborepo.com/docs/getting-started/installation#global-installation) installed (recommended)
turbo link

# Without [global `turbo`](https://turborepo.com/docs/getting-started/installation#global-installation), use your package manager
npx turbo link
yarn exec turbo link
pnpm exec turbo link
```

## Useful Links

Learn more about the power of Turborepo:

- [Tasks](https://turborepo.com/docs/crafting-your-repository/running-tasks)
- [Caching](https://turborepo.com/docs/crafting-your-repository/caching)
- [Remote Caching](https://turborepo.com/docs/core-concepts/remote-caching)
- [Filtering](https://turborepo.com/docs/crafting-your-repository/running-tasks#using-filters)
- [Configuration Options](https://turborepo.com/docs/reference/configuration)
- [CLI Usage](https://turborepo.com/docs/reference/command-line-reference)
