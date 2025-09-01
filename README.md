
모노레포 학습내용 (2025-08-26)

# 레포 구조

package.json
- 작업공간의 기반

root turbo.json
- wkrdjqrntjd 

### 자주 빠지는 함정
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
