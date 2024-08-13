# 1. Vercel 기본 사용법

## 1-1. Vercel 계정 만들기

Vercel 홈페이지에 접속하여 계정을 생성합니다. GitHub, GitLab, Bitbucket 계정으로 로그인할 수 있습니다.

<br><br><br>

## 1-2. 프로젝트 생성

2.1 Next.js 애플리케이션 생성

Next.js는 Vercel의 가장 최적화된 프레임워크입니다. 먼저 Next.js 애플리케이션을 생성합니다.

```bash
npx create-next-app@latest my-vercel-app
cd my-vercel-app
```

<br><br><br>

## 1-3. 프로젝트 파일 트리 구조

프로젝트를 생성하면 아래와 같은 기본적인 파일 트리 구조가 생성됩니다:

```lua
my-vercel-app/
│
├── public/
│   └── favicon.ico
├── pages/
│   ├── api/
│   │   └── hello.js
│   ├── _app.js
│   └── index.js
├── styles/
│   ├── globals.css
│   └── Home.module.css
├── .gitignore
├── package.json
├── README.md
├── next.config.js
└── vercel.json
```

<br><br><br>

## 1-4. 애플리케이션 개발

### 1-4-1. 서버리스 함수 추가

Vercel에서 서버리스 함수는 pages/api 디렉토리 아래에 생성합니다. 예를 들어, 간단한 API를 추가하려면

pages/api/hello.js:

```javascript
export default function handler(req, res) {
  res.status(200).json({ message: 'Hello from Vercel Serverless Function!' });
}
```

이 API는 /api/hello 경로로 접근할 수 있습니다.

<br>

### 1-4-2. 데이터베이스 연동

#### 1-4-2-1. 데이터베이스 클라이언트 설치

**PostgreSQL 클라이언트 설치**

```bash
npm install pg
```

<br>

**MariaDB 클라이언트 설치**

```bash
npm install mariadb
```

<br>

#### 1-4-2-2. 환경 변수 설정

.env.local 파일을 생성하여 데이터베이스 연결 문자열을 추가합니다:

**PostgreSQL**

```bash
DATABASE_URL=postgresql://username:password@localhost:5432/mydatabase
```

<br>

**MariaDB**

```bash
DATABASE_URL=mariadb://username:password@localhost:3306/mydatabase
```

<br><br>

#### 1-4-2-3. 서버리스 함수에서 데이터베이스 연동

데이터베이스에 연결하는 API를 생성합니다:

**pages/api/db.js**

```javascript
import { Client } from 'pg'; // PostgreSQL 클라이언트
// import mariadb from 'mariadb'; // MariaDB 클라이언트 사용 시 이 라인 주석 해제

export default async function handler(req, res) {
  const client = new Client({
    connectionString: process.env.DATABASE_URL,
  });

  try {
    await client.connect();
    const result = await client.query('SELECT NOW()');
    res.status(200).json({ time: result.rows[0] });
  } catch (error) {
    res.status(500).json({ error: 'Database connection error' });
  } finally {
    await client.end();
  }
}
```

<br><br><br>

## 1-5. 로컬에서 테스트 및 개발

로컬 개발 서버를 시작합니다:

```bash
npm run dev
```

웹 브라우저에서 http://localhost:3000에 접속하여 애플리케이션을 테스트합니다.

<br><br><br>

## 1-6. 배포

### 1-6-1. Vercel CLI를 통한 배포

**Vercel CLI 설치**

Vercel CLI를 설치합니다:

```bash
npm install -g vercel
```

<br>

**Vercel 로그인 및 배포**

Vercel에 로그인합니다:

```bash
vercel login
```

<br>

**프로젝트 디렉토리에서 배포**

```bash
vercel
```

배포 과정에서 몇 가지 질문이 나오며, 기본 설정을 선택하거나 필요한 정보를 입력합니다.

<br><br>

### 1-6-2. Vercel Dashboard를 통한 배포

Vercel Dashboard에 로그인합니다.
"New Project" 버튼을 클릭하고 GitHub, GitLab, 또는 Bitbucket 저장소를 연결합니다.
프로젝트를 선택하고 "Import" 버튼을 클릭하여 프로젝트를 Vercel에 추가합니다.
Vercel은 자동으로 배포를 수행하며, 환경 변수를 설정할 수 있는 옵션도 제공합니다.

<br><br><br>

## 1-7. Vercel 설정 파일

vercel.json 파일을 사용하여 배포 설정을 구성할 수 있습니다. 기본적인 설정 파일은 다음과 같습니다:

```json
{
  "version": 2,
  "builds": [
    { "src": "package.json", "use": "@vercel/node" }
  ],
  "routes": [
    { "src": "/api/(.*)", "dest": "/pages/api/$1.js" }
  ]
}
```

이 파일은 빌드 및 라우팅 구성을 정의합니다.

<br><br><br>

## 1-8. 프로젝트 프로세스

### 1-8-1. 프로젝트 생성

```bash
npx create-next-app my-vercel-app
```

<br><br>

### 1-8-2. 개발

서버리스 함수 추가: pages/api/ 디렉토리 아래에 함수 생성
데이터베이스 연동: 클라이언트 설치 및 .env.local 파일에 환경 변수 추가

<br><br>

### 1-8-3. 테스트

**로컬 서버 실행 **

```bash
npm run dev
```

<br><br>

### 1-8-4. 배포

Vercel CLI (vercel 명령어) 또는 Vercel Dashboard를 통해 배포

<br><br>

## 1-8-5. 배포 후 관리

Vercel Dashboard에서 배포된 프로젝트를 관리할 수 있으며, 환경 변수 추가, 배포 상태 모니터링, 로그 확인 등의 작업을 수행할 수 있습니다.
