# Questioning (意为“质疑”)

**“质疑是超越之始。” —— 我们相信，通过重新审视和构建Web开发的基本原则，能够创造出更和谐、更语义化、更具可维护性的应用程序。**

## 核心理念：系统和谐优先 (Systemic Harmony First)

在当前的Web开发浪潮中，组件化带来了前所未有的模块化和复用性。然而，过度强调单个组件的独立封装，有时会牺牲应用的整体一致性和设计语言的连贯性，导致：

*   **设计碎片化：** 相似的交互和视觉元素在不同组件中可能存在细微但令人困惑的差异。
*   **维护成本增加：** 全局设计变更（如主题色、禁用状态样式）需要深入到众多独立组件中进行修改。
*   **语义丢失：** 样式和行为往往与特定组件紧密耦合，而非更通用的语义概念（如“一个被选中的元素”、“一个只读的输入区”）。

**Questioning 旨在扭转这一趋势。** 我们认为，一个优秀的框架应该将**设计系统和通用交互原则**置于核心，使开发者能够构建出在视觉和行为上高度统一的应用程序。组件仍然是构建UI的基本单元，但它们将作为消费和体现这些系统级原则的“公民”，而非孤立的“孤岛”。

**我们推崇：**

*   **原则驱动 (Principle-Driven):** 框架的核心是可配置的设计原则和交互模式，而非仅仅是UI组件库。
*   **语义优先 (Semantics First):** 样式和行为应尽可能附着于HTML的语义属性（如 `disabled`, `readonly`）、ARIA属性或统一的 `data-*` 状态属性，而非特定的CSS类名。
*   **CSS变量为基石 (CSS Variables as Bedrock):** 所有设计令牌（颜色、间距、字体、圆角等）通过CSS变量在全局定义和管理，实现轻松的主题化和一致性。
*   **分层设计 (Layered Design):** 借鉴CSS `@layer` 的思想，框架的样式和逻辑也分为不同层次（基础、语义、组件、工具），确保清晰的优先级和可控性。
*   **DRY (Don't Repeat Yourself):** 通用的样式和行为逻辑在框架层面统一处理，开发者只需关注业务特定部分。

## 设计逻辑与流程概览

Questioning 的设计逻辑围绕以下几个核心层面展开：

### 1. 基础层 (Foundation Layer) - 设计令牌与全局设定

*   **CSS:**
    *   **主题与变量 (`theme.css` / JS模块):** 定义所有CSS自定义属性（设计令牌），如 `--Questioning-color-primary`, `--Questioning-spacing-md`, `--Questioning-radius-default`。这些变量是整个系统视觉一致性的源头。
    *   **全局重置与基础样式 (`reset.css`, `base.css`):** 标准化的浏览器重置，以及针对 `body`, `a`, `p` 等基础元素的全局样式，均使用上述变量。
    *   **`@layer base;`**: 这些样式会被置于最低的CSS层叠优先级。
*   **JavaScript:**
    *   **全局配置对象:** 用于设定框架级别的参数，如国际化默认语言、API端点前缀等（如果适用）。

### 2. 语义层 (Semantics Layer) - 通用原则的实现

这是 Questioning 最具特色的层面，它定义了**与具体组件无关的、基于语义和状态的通用样式与行为**。

*   **CSS (`semantics.css`):**
    *   **属性选择器驱动:** 大量使用属性选择器来应用通用样式。
        ```css
        @layer semantics {
          /* 禁用状态 */
          [disabled], .Questioning-is-disabled {
            opacity: var(--Questioning-opacity-disabled);
            cursor: not-allowed;
            /* ...更多通用禁用样式 */
          }

          /* 只读状态 */
          [readonly] {
            background-color: var(--Questioning-bg-readonly);
            /* ...更多通用只读样式 */
          }

          /* 选中/激活状态 (示例) */
          [aria-selected="true"], [aria-current="page"],
          .Questioning-selection-container:has(input:checked) { /* 需浏览器支持 :has() 或JS辅助 */
            outline: var(--Questioning-outline-selected-width) solid var(--Questioning-color-selected-ring);
            box-shadow: var(--Questioning-shadow-selected);
          }
          /* ...更多基于ARIA状态、data-*属性的通用样式 */
        }
        ```
    *   **结构模式:** 为常见的UI结构（如标签组、输入框附加元素）提供基础的结构性样式，这些样式是轻量且可覆盖的。
*   **JavaScript (`semantics.js` / 行为模块):**
    *   **行为附加器 (Behavior Attachers):** 框架会监听特定的 `data-Questioning-behavior` 属性或通用语义属性，自动附加标准化的JS行为。
        ```javascript
        // 示例：通用可折叠区域行为
        // HTML: <button data-Questioning-toggle="#collapsible-section">Toggle</button>
        //       <div id="collapsible-section" class="Questioning-is-collapsed">...</div>
        Questioning.registerBehavior('data-Questioning-toggle', (element) => {
          const targetSelector = element.getAttribute('data-Questioning-toggle');
          const target = document.querySelector(targetSelector);
          if (target) {
            element.addEventListener('click', () => {
              target.classList.toggle('Questioning-is-collapsed');
              target.classList.toggle('Questioning-is-expanded');
              // 更新 aria-expanded 等属性
            });
          }
        });
        ```
    *   **状态同步:** 确保JS行为与CSS可见状态（通过类名或`data-*`属性）同步。

### 3. 组件层 (Components Layer) - 组装与特化

*   **CSS (`components/*.css`):**
    *   组件将**主要消费**基础层定义的CSS变量和语义层提供的通用样式。
    *   组件自身的CSS主要负责其**独有的内部布局、结构特定样式，以及对语义层样式的微调或扩展**。
    *   推荐使用CSS Modules或类似作用域方案，并将组件样式放入 `@layer components;`。
        ```css
        /* Button.module.css */
        @layer components {
          .button {
            /* 消费全局变量 */
            padding: var(--Questioning-spacing-sm) var(--Questioning-spacing-md);
            background-color: var(--Questioning-color-primary);
            color: var(--Questioning-color-on-primary);
            border-radius: var(--Questioning-radius-button);
            /* 可能会有少量特定于按钮结构的样式 */
          }

          /* 按钮的特定状态（如果语义层不足以覆盖） */
          .button.isLoading {
            /* ...特定加载样式 */
          }
        }
        ```
*   **JavaScript (`components/*.js`):**
    *   组件的JS逻辑主要负责其内部状态管理、事件处理以及与业务逻辑的交互。
    *   应尽可能复用语义层提供的通用行为。例如，一个自定义的下拉菜单组件，其展开/折叠行为可能就利用了语义层提供的 `data-Questioning-toggle` 行为。

### 4. 工具层 (Utilities Layer) - 按需辅助 (可选)

*   **CSS (`utilities.css`):**
    *   提供原子化的、单一职责的工具类（如 `Questioning-mt-sm` 代表 `margin-top: var(--Questioning-spacing-sm);`），用于快速微调布局和样式。
    *   这些工具类应置于较高优先级的 `@layer utilities;`。
*   **JavaScript (`utils/*.js`):**
    *   提供纯函数式的辅助工具，如日期格式化、DOM操作助手等。

## 开发流程设想

1.  **定义设计系统：** 首先，团队（设计师与开发者）共同确定核心设计令牌和通用交互原则。
2.  **配置Questioning基础层：** 将设计令牌转化为CSS变量，配置全局基础样式。
3.  **实现Questioning语义层：** 根据交互原则，编写通用的CSS规则和JS行为。这是框架的核心价值所在。
4.  **开发组件：**
    *   开发者在构建组件时，首先考虑如何利用HTML语义和框架提供的语义层规则来满足大部分样式和行为需求。
    *   为组件编写少量必要的、特定于其结构和独特功能的CSS和JS。
    *   大量使用设计令牌（CSS变量）。
5.  **组装页面与应用：** 使用开发好的组件构建完整的用户界面。
6.  **迭代与演进：** 当出现新的通用模式时，优先考虑将其抽象并添加到Questioning的语义层，而非在多个组件中重复实现。

## 我们的目标

Questioning 不仅仅是一个UI库或CSS框架，它是一种**构建Web应用的方法论**。我们旨在提供一套工具和理念，帮助团队：

*   构建出在视觉和交互上高度一致的应用程序。
*   降低因设计变更带来的维护成本。
*   编写更具语义、更易理解的代码。
*   提升开发体验，让开发者更专注于业务逻辑，而非重复的样式和行为实现。

---

**[项目状态：概念与早期探索阶段]**

我们深知这是一条与主流组件封装思想有所差异的道路，但我们相信，对于追求极致设计一致性和长期可维护性的项目而言，这套理念具有巨大的潜力。欢迎有相同理念的开发者一同探讨、质疑和共建！