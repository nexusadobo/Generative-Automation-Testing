# project_setup.md

## 🚀 GitHub Copilot + VS Code Agent Mode - Playwright BDD Template

### 🤖 Configuración del Proyecto con IA Integrada
**Template optimizado para VS Code's Agent Mode y GitHub Copilot for Playwright Code Generation**

Este proyecto combina:
- **🎯 GitHub Copilot** - Generación inteligente de código
- **🤖 VS Code Agent Mode** - Contexto completo del proyecto
- **🎭 Playwright-BDD** - Testing framework BDD
- **🔗 MCP (Model Context Protocol)** - Integración IA-Browser

### 📋 Requisitos Previos

- **Node.js**: 18 o superior
- **VS Code**: Última versión con **Agent Mode habilitado**
- **GitHub Copilot**: Suscripción activa
- **Git**: Configurado y funcional
- **Terminal**: PowerShell (Windows) o Bash (Linux/macOS)

### 🏗️ Estructura de Proyecto

```
proyecto-e2e/
├── .vscode/
│   └── settings.json
├── docs/
│   ├── CHANGELOG.md
│   ├── ARCHITECTURE.md
│   └── PATTERNS.md
├── e2e/
│   └── (archivos auto-generados)
├── features/
│   └── *.feature
├── fixtures/
│   └── *.ts
├── pages/
│   └── *.ts
├── steps/
│   ├── common/
│   └── *.steps.ts
├── utils/
│   └── *.ts
├── .gitignore
├── package.json
├── playwright.config.ts
└── README.md
```

## 📦 Instalación Paso a Paso

### 1. Crear y Configurar Proyecto

```bash
# Crear directorio del proyecto
mkdir proyecto-e2e
cd proyecto-e2e

# Inicializar proyecto Node.js
npm init -y
```

### 2. Instalar Dependencias

```bash
# Instalar Playwright y Playwright-BDD
npm install --save-dev @playwright/test playwright-bdd

# Instalar browsers
npx playwright install

# Instalar MCP Playwright (si no está instalado globalmente)
npm install --save-dev @playwright/mcp
```

### 3. Configurar VS Code para GitHub Copilot + Agent Mode + MCP

#### Habilitar Agent Mode en VS Code:
1. Abrir VS Code
2. `Ctrl/Cmd + Shift + P` → "GitHub Copilot: Enable Agent Mode"
3. Verificar que Agent Mode esté activo

#### Crear `.vscode/settings.json`:
```json
{
  "github.copilot.enable": {
    "*": true,
    "yaml": true,
    "plaintext": true,
    "markdown": true,
    "gherkin": true
  },
  "github.copilot.advanced": {
    "debug.overrideEngine": "copilot-agent"
  },
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp@latest", "--headless"]
    }
  },
  "playwright-bdd.cucumber.glue": ["steps/**/*.ts"],
  "playwright-bdd.cucumber.features": ["features/**/*.feature"],
  "copilot.enable": {
    "playwright": true,
    "bdd": true,
    "gherkin": true
  }
}
```

#### Configurar GitHub Copilot para Playwright:
```json
// .vscode/copilot-instructions.md
# GitHub Copilot Instructions for Playwright BDD

## Context
This project uses Playwright-BDD with MCP integration for AI-powered test generation.

## Patterns to Follow
- Use semantic selectors (getByRole, getByText) over XPath
- Generate BDD features first, then step definitions
- Implement Page Object Models for repeated patterns
- Document all helpers and patterns

## Test Structure
- Features in /features/*.feature
- Steps in /steps/*.steps.ts
- Page Objects in /pages/*.ts
- Helpers in /utils/*.ts
```

#### Instalar extensiones recomendadas para IA:
```bash
# Extensiones esenciales para GitHub Copilot + Playwright
code --install-extension GitHub.copilot
code --install-extension GitHub.copilot-chat
code --install-extension ms-playwright.playwright
code --install-extension alexkrechik.cucumberautocomplete
code --install-extension bradlc.vscode-tailwindcss
```

### 4. Configurar Playwright

#### Crear `playwright.config.ts`:
```typescript
import { defineConfig } from '@playwright/test';
import { defineBddConfig } from 'playwright-bdd';

const testDir = defineBddConfig({
  importTestFrom: 'steps/fixtures.ts',
  paths: ['features/**/*.feature'],
  require: ['steps/**/*.ts'],
  quoteless: true,
});

export default defineConfig({
  testDir,
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: [
    ['html'],
    ['json', { outputFile: 'test-results.json' }],
    ['junit', { outputFile: 'test-results.xml' }]
  ],
  use: {
    baseURL: 'https://www.ferrovial.com',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
    {
      name: 'mobile-chrome',
      use: { ...devices['Pixel 5'] },
    },
  ],
});
```

### 5. Crear Fixtures Base

#### Crear `steps/fixtures.ts`:
```typescript
import { test as base } from 'playwright-bdd';
import { HomePage } from '../pages/HomePage';
import { NavigationHelper } from '../utils/navigation';

export const test = base.extend<{
  homePage: HomePage;
  navigationHelper: NavigationHelper;
}>({
  homePage: async ({ page }, use) => {
    const homePage = new HomePage(page);
    await use(homePage);
  },
  navigationHelper: async ({ page }, use) => {
    const navigationHelper = new NavigationHelper(page);
    await use(navigationHelper);
  },
});

export { expect } from '@playwright/test';
```

### 6. Configurar Scripts de Package.json (AI-Optimized)

#### Actualizar `package.json`:
```json
{
  "name": "playwright-bdd-mcp-template",
  "description": "AI-powered Playwright BDD template with GitHub Copilot & VS Code Agent Mode",
  "scripts": {
    "test": "npx bddgen && npx playwright test",
    "test:headed": "npx bddgen && npx playwright test --headed",
    "test:debug": "npx bddgen && npx playwright test --debug",
    "test:ui": "npx bddgen && npx playwright test --ui",
    "generate": "npx bddgen",
    "report": "npx playwright show-report",
    "install-browsers": "npx playwright install",
    "copilot-setup": "echo 'GitHub Copilot configured for Playwright BDD'",
    "ai-validate": "npx @playwright/mcp@latest --headless",
    "docs:generate": "echo 'Auto-generating documentation with AI'"
  },
  "keywords": [
    "playwright",
    "bdd",
    "github-copilot",
    "vs-code-agent",
    "mcp",
    "ai-testing",
    "e2e"
  ]
}
```

### 7. Configurar Git

#### Crear `.gitignore`:
```gitignore
# Dependencies
node_modules/

# Test results
test-results/
playwright-report/
playwright/.cache/

# Generated files
e2e/
.features-gen/

# OS files
.DS_Store
Thumbs.db

# IDE
.vscode/extensions.json
.idea/

# Logs
*.log
npm-debug.log*
```

### 8. Crear Estructura de Directorios

#### Comando multiplataforma:
```bash
# Windows (PowerShell) y Linux/macOS
mkdir -p docs features fixtures pages steps/common utils
```

#### O individualmente:
```bash
mkdir docs
mkdir features
mkdir fixtures
mkdir pages
mkdir steps
mkdir steps/common
mkdir utils
```

### 9. Crear Archivos de Documentación Iniciales

#### `docs/CHANGELOG.md`:
```markdown
# Changelog - AI-Powered Playwright BDD Template

## [Unreleased]

### Added
- ✅ **GitHub Copilot** integration para generación automática de código
- ✅ **VS Code Agent Mode** configuración para contexto completo
- ✅ **MCP Playwright** para validación en tiempo real
- ✅ Configuración inicial del proyecto con IA optimizada
- ✅ Estructura base de directorios para AI-assisted development
- ✅ Templates y prompts para GitHub Copilot
- ✅ Auto-documentation capabilities

### Changed
- 🔄 Workflow optimizado para **Agent Mode + Copilot**
- 🔄 Step definitions con **AI-completion** habilitado

### Fixed
- N/A

### AI Features
- 🤖 **Auto-generation** de features desde descripción natural
- 🤖 **Smart completion** para step definitions
- 🤖 **Pattern detection** automático para refactorización
- 🤖 **Real-time validation** con MCP integration
```

#### `docs/ARCHITECTURE.md`:
```markdown
# Arquitectura del Proyecto

## Estructura de Directorios

- `/features/` - Archivos .feature con escenarios BDD
- `/steps/` - Step definitions en TypeScript
- `/steps/common/` - Steps reutilizables
- `/pages/` - Page Object Models
- `/utils/` - Helper functions
- `/fixtures/` - Configuración personalizada
- `/docs/` - Documentación del proyecto
```

#### `README.md`:
```markdown
# Playwright BDD + MCP Template - GitHub Copilot Optimized

## 🤖 AI-Powered E2E Testing

Template optimizado para **VS Code Agent Mode** y **GitHub Copilot for Playwright Code Generation**.

### Características
- ✅ **GitHub Copilot** integrado para generación de código
- ✅ **VS Code Agent Mode** para contexto completo
- ✅ **Playwright-BDD** para testing BDD
- ✅ **MCP** para validación automática
- ✅ **AI-assisted** test creation y maintenance

## Instalación

```bash
npm install
npm run install-browsers
```

## Uso con GitHub Copilot

1. **Activar Agent Mode**: `Ctrl+Shift+P` → "GitHub Copilot: Enable Agent Mode"
2. **Crear Feature**: Usar Copilot Chat para generar .feature files
3. **Generar Steps**: Copilot auto-completa step definitions
4. **Validar**: MCP valida automáticamente con browser real

## Ejecución

```bash
npm test                # Ejecutar todos los tests
npm run test:headed     # Ejecutar con interfaz gráfica
npm run test:debug      # Ejecutar en modo debug
npm run test:ui         # Ejecutar con UI de Playwright
```

## Comandos IA-Optimizados

```bash
npm run generate        # Generar tests desde features (con IA)
npm run report          # Ver reporte HTML
npm run copilot-setup   # Configurar GitHub Copilot para el proyecto
```

## Workflow con IA

1. **Describe** el escenario en lenguaje natural a Copilot
2. **Genera** el .feature file automáticamente
3. **Implementa** steps con auto-complete de Copilot
4. **Valida** con MCP en tiempo real
5. **Refactoriza** con sugerencias de Agent Mode
```

### 10. Verificar Instalación

```bash
# Generar tests de prueba
npm run generate

# Ejecutar tests (debería pasar sin errores)
npm test

# Verificar que MCP funciona
npx @playwright/mcp@latest --help
```

## 🔧 Comandos Multiplataforma

### Windows (PowerShell):
```powershell
# Crear proyecto
New-Item -ItemType Directory -Path "proyecto-e2e"
Set-Location "proyecto-e2e"

# Verificar Node.js
node --version
npm --version
```

### Linux/macOS (Bash):
```bash
# Crear proyecto
mkdir proyecto-e2e
cd proyecto-e2e

# Verificar Node.js
node --version
npm --version
```

## ✅ Verificación Final (AI-Enabled)

- [ ] Proyecto inicializado correctamente
- [ ] **GitHub Copilot** activado y funcionando
- [ ] **VS Code Agent Mode** habilitado
- [ ] Dependencias instaladas
- [ ] Browsers de Playwright instalados
- [ ] **MCP integration** configurada y validada
- [ ] Estructura de directorios creada
- [ ] Archivos de configuración en su lugar
- [ ] **Copilot instructions** configuradas
- [ ] Tests de ejemplo ejecutándose con **IA assistance**
- [ ] Documentación inicial creada

## 🚨 Troubleshooting (AI-Assisted)

### Problema: GitHub Copilot no funciona
```bash
# Verificar suscripción y reactivar
code --install-extension GitHub.copilot
# En VS Code: Ctrl+Shift+P → "GitHub Copilot: Sign In"
```

### Problema: Agent Mode no disponible
```bash
# Actualizar VS Code a la versión más reciente
# Verificar que tienes GitHub Copilot activo
# Reiniciar VS Code después de la actualización
```

### Problema: MCP no funciona en VS Code
```bash
# Reinstalar MCP con configuración específica para Agent Mode
npm install --save-dev @playwright/mcp@latest
# Verificar configuración en .vscode/settings.json
# Reiniciar VS Code
```

### Problema: Copilot no sugiere código Playwright
```bash
# Verificar .vscode/copilot-instructions.md
# Asegurar que las extensiones están instaladas
# Reiniciar el servicio de Copilot: Ctrl+Shift+P → "Reload Window"
```

### Problema: Browsers no instalados
```bash
# Instalar browsers explícitamente
npx playwright install chromium firefox webkit
```

### Problema: Permisos en Linux/macOS
```bash
# Dar permisos de ejecución
chmod +x node_modules/.bin/playwright
```
