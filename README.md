# Questioning (意为“质疑”)

**“质疑是超越之始。” —— 我们相信，通过重新审视和构建 Web 开发的基本原则，能够创造出更和谐、更语义化、更具可维护性、真正以用户为中心的应用程序。**

在现代 Web 开发中，组件化无疑带来了巨大的进步。然而，当我们过度聚焦于单个组件的独立性时，应用的整体性和体验的连贯性往往会受到挑战。我们是否常常遇到：

*   **设计语言的断裂：** 相似功能在不同组件、不同页面，甚至不同团队的产出中，呈现出令人困惑的视觉和交互差异？
*   **维护的噩梦：** 一个全局性的设计调整（如品牌色升级、圆角规范变更、禁用状态的统一视觉）需要深入到无数个孤立组件中逐一修改，耗时耗力且容易遗漏？
*   **语义的迷失：** 样式和行为往往与特定组件紧密耦合，而非更通用的语义概念（如“一个被选中的元素”、“一个只读的输入区”）。而忽略了 HTML 元素本身或 ARIA 属性所承载的丰富语义，使得代码难以理解，可访问性降低？

## 核心理念：系统和谐优先 (Systemic Harmony First)

**Questioning 旨在系统性地解决这些问题。** 我们主张，一个卓越的开发框架，应将**统一的设计系统、通用的交互原则和丰富的语义表达**置于其架构的核心。组件依然是构建用户界面的基石，但它们不再是各自为政的“孤岛”，而是作为消费和体现这些系统级原则的“良好公民”，共同构筑和谐、一致、可维护的数字产品。

**我们推崇：**

*   **原则驱动 (Principle-Driven):** 框架的核心是**可配置、可扩展的设计原则和交互模式**的集合，而非仅仅是一个封闭的 UI 组件库。这些原则指导着从设计令牌到具体组件行为的每一个细节。例如，定义“即时反馈原则”、“状态可见性原则”等，并在框架层面提供实现这些原则的机制。
*   **语义优先 (Semantics First):** 样式和行为应尽可能附着于HTML的语义属性（如 `disabled`, `readonly`）、ARIA属性或统一的 `data-*` 状态属性，而非特定的CSS类名来描述界面元素的状态和功能。样式和行为应尽可能附着于这些语义标记或统一的状态属性，而非不透明的、仅用于样式的 CSS 类名。这不仅提升了代码的可读性和可维护性，也为搜索引擎优化 (SEO) 和可访问性 (a11y) 打下坚实基础。
*   **CSS变量为基石 (CSS Variables as Bedrock):** 所有设计令牌（如颜色、间距、字体、圆角、动画时长等）通过 CSS 自定义属性在全局范围内定义和管理。这为实现**轻松的主题化、动态换肤、高对比度模式以及确保设计语言在整个应用中的严格一致性**提供了强大支持。
*   **分层设计 (Layered Design):** 借鉴 CSS `@layer` 的思想，框架的样式和逻辑被精心划分为不同的层级（例如：`base` -> `semantics` -> `components` -> `utilities`）。这种分层确保了清晰的优先级管理、样式的可预测覆盖以及更强的模块化和可维护性。每一层都有其明确的职责，使得开发者能够更好地理解和控制样式的应用。
*   **DRY (Don't Repeat Yourself) 与关注点分离 (Separation of Concerns):** **通用的样式逻辑和标准化的交互行为在框架的语义层进行统一处理和封装**。开发者在构建具体业务组件时，可以专注于组件独有的业务逻辑和表现细节，而将通用的状态管理、交互模式等交由框架处理，从而显著减少重复代码，提升开发效率。

## 设计逻辑与流程概览

Questioning 的设计逻辑围绕以下几个核心层面展开，它们协同工作，共同构建出和谐统一的应用体验：

### 1. 基础层 (Foundation Layer) - 设计令牌与全局设定

此层定义了整个应用的视觉和行为基调。

*   **CSS:**
    *   **主题与变量 (`theme.css` / JS模块):** 定义所有CSS自定义属性（设计令牌），如 `--Questioning-color-primary`, `--Questioning-spacing-md`, `--Questioning-radius-default`。这些变量是整个系统视觉一致性的**唯一真实来源 (Single Source of Truth)**。
    *   **全局重置与基础样式 (`reset.css`, `base.css`):** 标准化的浏览器重置，以及针对 `body`, `a`, `p` 等基础元素的全局样式，确保跨浏览器的一致性。
    *   **`@layer base;`**: 这些样式会被置于 CSS 层叠的最低优先级，确保它们可以被后续层级轻松覆盖和扩展。
*   **JavaScript:**
    *   **全局配置对象:** 用于设定框架级别的参数，如国际化默认语言、API端点前缀等（如果适用）。

### 2. 语义层 (Semantics Layer) - 通用原则的实现

**这是 Questioning 最具创新性和核心价值的层面。它致力于将抽象的设计原则和交互模式，转化为与具体组件形态解耦的、基于 HTML 语义和标准属性的通用样式与行为。**。

*   **CSS (`semantics.css`):**
    *   **属性选择器驱动:** 大量、智能地使用 HTML 属性选择器（包括原生属性、ARIA 属性、以及框架约定的属性）来应用通用样式。
        ```css
        @layer semantics {
          /* 禁用状态 - 适用于所有可禁用元素 */
          [disabled],
          [aria-disabled="true"],
          .Questioning-is-disabled, /* 提供一个class作为备选方案 */
          [data-Questioning-state-disabled="true"] {
            opacity: var(--Questioning-opacity-disabled);
            cursor: not-allowed;
            /* ...更多通用禁用视觉反馈, 如滤镜、特定背景色 */
            /* 考虑用户代理样式表已有的禁用样式，进行增强而非完全覆盖 */
          }

          /* 只读状态 - 适用于表单元素 */
          [readonly],
          [data-Questioning-state-readonly="true"] {
            background-color: var(--Questioning-bg-readonly, var(--Questioning-color-gray-100)); /* 允许主题覆盖 */
            border-style: var(--Questioning-border-style-readonly, dotted);
            /* ...更多通用只读视觉反馈 */
          }

          /* 选中/激活状态 (示例) - 适用于导航、选项卡、列表项等 */
          [aria-selected="true"],
          [aria-current="page"], /* 导航当前页 */
          [aria-current="step"], /* 步骤条当前步骤 */
          .Questioning-is-selected,
          [data-Questioning-state-selected="true"] {
            background-color: var(--Questioning-color-selected-bg, var(--Questioning-color-primary-lightest));
            color: var(--Questioning-color-selected-text, var(--Questioning-color-primary-dark));
            outline: var(--Questioning-outline-selected-width, 2px) solid var(--Questioning-color-selected-ring, var(--Questioning-color-primary));
            outline-offset: var(--Questioning-outline-offset-selected, 2px);
            box-shadow: var(--Questioning-shadow-selected, none);
          }

          /* 错误状态 (示例) - 适用于表单输入、容器等 */
          [aria-invalid="true"],
          .Questioning-has-error,
          [data-Questioning-state-error="true"] {
            border-color: var(--Questioning-color-error-border, var(--Questioning-color-danger));
            background-color: var(--Questioning-color-error-bg, var(--Questioning-color-danger-lightest));
          }
          [aria-invalid="true"] ~ .Questioning-error-message { /* 配合错误提示信息 */
            color: var(--Questioning-color-error-text, var(--Questioning-color-danger-dark));
          }

          /* 拥有焦点的容器 (示例，需要 :focus-within 支持) */
          .Questioning-focus-container:focus-within {
            box-shadow: 0 0 0 var(--Questioning-focus-ring-width, 3px) var(--Questioning-color-focus-ring, rgba(var(--Questioning-color-primary-rgb), 0.3));
          }

          /* 通用可折叠区域的展开/收起视觉状态 */
          .Questioning-is-collapsed > .Questioning-collapsible-content {
            display: none; /* 或更平滑的动画隐藏 */
          }
          .Questioning-is-expanded > .Questioning-collapsible-content {
            display: block; /* 或动画展开 */
          }
          [aria-expanded="false"] + .Questioning-collapsible-content-controlled-by-aria {
            /* ... */
          }
          [aria-expanded="true"] + .Questioning-collapsible-content-controlled-by-aria {
            /* ... */
          }

          /* ...更多基于ARIA状态 (aria-pressed, aria-busy, etc.)、data-Questioning-* 属性的通用样式 */
        }
        ```
    *   **结构模式:** 为常见的、非特定组件的 UI 结构（如标签组 ul[role="tablist"] > li[role="tab"]、输入框附加前后缀的布局、响应式网格布局基类）提供基础的、轻量的、可覆盖的结构性样式。这些样式应尽可能不涉及装饰性视觉，只负责骨架。
*   **JavaScript (`semantics.js` / 行为模块):**
    *   **行为附加器 (Behavior Attachers):** 框架会自动或按需扫描 DOM，监听特定的属性或通用的语义属性 (如 aria-controls 结合 aria-expanded)，为匹配的元素自动附加标准化的、可访问的 JS 行为。
        ```javascript
        // 示例：通用可折叠区域行为 (Toggle Behavior)
        // HTML:
        // <button
        //   data-Questioning-behavior-toggle
        //   aria-controls="collapsible-section-1"
        //   aria-expanded="false"
        // >Toggle Section 1</button>
        // <div id="collapsible-section-1" class="Questioning-collapsible-content">...</div>

        Questioning.Behaviors.register('toggle', {
          selector: '[data-Questioning-behavior-toggle][aria-controls]',
          init: (triggerElement) => {
            const targetId = triggerElement.getAttribute('aria-controls');
            const targetElement = document.getElementById(targetId);

            if (!targetElement) {
              console.warn(`Questioning.Behaviors.toggle: Target element with ID "${targetId}" not found.`, triggerElement);
              return;
            }

            // 初始化状态 (从 aria-expanded 读取或默认)
            let isExpanded = triggerElement.getAttribute('aria-expanded') === 'true';
            targetElement.style.display = isExpanded ? '' : 'none'; // 或者用class控制

            const toggle = () => {
              isExpanded = !isExpanded;
              triggerElement.setAttribute('aria-expanded', String(isExpanded));
              targetElement.style.display = isExpanded ? '' : 'none'; // 或切换class
              // 更好的做法是切换 .Questioning-is-collapsed / .Questioning-is-expanded 类
              // targetElement.classList.toggle('Questioning-is-collapsed', !isExpanded);
              // targetElement.classList.toggle('Questioning-is-expanded', isExpanded);

              // 触发自定义事件，方便外部监听
              triggerElement.dispatchEvent(new CustomEvent('Questioning:toggled', {
                bubbles: true,
                detail: { target: targetElement, expanded: isExpanded }
              }));
            };

            triggerElement.addEventListener('click', toggle);

            // 提供卸载/清理机制
            return () => {
              triggerElement.removeEventListener('click', toggle);
              // 还原对targetElement的修改 (如果需要)
            };
          }
        });

        // 框架在DOMContentLoaded或特定时机调用
        // Questioning.Behaviors.scanAndApply(document.body);
        ```
    *   **状态同步:** 确保 JS 行为在改变元素状态时，同步更新相关的 ARIA 属性 (如 `aria-expanded`, `aria-hidden`, `aria-selected`) 和 `data-Questioning-state-*` 属性，从而保持与 CSS 视觉状态的一致性，并提升可访问性。

### 3. 组件层 (Components Layer) - 组装与特化

组件是用户界面的具体实现，它们消费基础层和语义层的能力，并添加自身特有的结构和逻辑。

*   **CSS (`components/*.css`):**
    *   组件的样式**首要任务是消费基础层定义的 CSS 变量**来实现其视觉表现。
    *   组件应**尽可能利用语义层提供的通用样式和状态规则**。例如，一个按钮组件的禁用样式应该直接由语义层的 `[disabled]` 规则提供，组件自身无需重复定义。
    *   组件自身的CSS主要负责其**内部独有的布局、结构相关的特定样式，以及对语义层通用样式的微调、扩展或在特定上下文中的覆盖**。
    *   推荐使用作用域方案来避免样式冲突，并将组件样式放入 `@layer components;`, 其优先级高于 `semantics` 层但低于 `utilities` 层。
        ```css
        /* components/Button.module.css */
        @layer components {
          .button {
            /* 消费全局变量 */
            display: inline-flex; /* 内部布局 */
            align-items: center;
            justify-content: center;
            padding: var(--Questioning-spacing-sm) var(--Questioning-spacing-md);
            font-family: var(--Questioning-font-family-button, var(--Questioning-font-family-base)); /* 可覆盖的基础字体 */
            font-weight: var(--Questioning-font-weight-button, var(--Questioning-font-weight-semibold));
            background-color: var(--Questioning-color-primary);
            color: var(--Questioning-color-on-primary); /* 确保对比度 */
            border: var(--Questioning-border-width-button, 1px) solid var(--Questioning-color-primary-border, transparent);
            border-radius: var(--Questioning-radius-button, var(--Questioning-radius-default));
            text-decoration: none;
            cursor: pointer;
            transition: background-color var(--Questioning-duration-fast) var(--Questioning-easing-standard),
                        color var(--Questioning-duration-fast) var(--Questioning-easing-standard);
            /* 继承语义层的 [disabled] 样式, 但可以有特定微调 */
          }

          .button:hover:not([disabled]):not([aria-disabled="true"]) {
            background-color: var(--Questioning-color-primary-hover, var(--Questioning-color-primary-dark));
          }

          .button:active:not([disabled]):not([aria-disabled="true"]) {
            background-color: var(--Questioning-color-primary-active, var(--Questioning-color-primary-darker));
          }

          /* 按钮的特定变体或状态 (如果语义层不足以完全覆盖或需要更强的组件特有视觉) */
          .button.isSecondary {
            background-color: var(--Questioning-color-secondary);
            color: var(--Questioning-color-on-secondary);
            /* ... */
          }

          .button.isLoading > .iconSlot { /* 假设按钮内有图标插槽 */
            animation: Questioning-spin var(--Questioning-duration-loading-spin, 1s) linear infinite;
          }
        }

        /* 可以在基础层或工具层定义 @keyframes Questioning-spin */
        ```
*   **JavaScript (`components/*.js`):**
    *   组件的 JS 逻辑主要负责其**内部复杂状态管理、特定事件处理（超出语义行为覆盖范围的）、与业务逻辑的深度交互、以及与其他组件的协调**。
    *   **应尽可能复用或组合语义层提供的通用行为模块**。例如，一个自定义的下拉菜单组件，其核心的展开/折叠行为可能直接利用了语义层提供的 `data-Questioning-behavior-toggle` 行为和相关的 ARIA 管理，组件JS只需处理选项选择、数据加载等更具体的逻辑。
    *   组件应提供清晰的 API 接口供外部调用和配置。

### 4. 工具层 (Utilities Layer) - 按需辅助 (可选)

提供细粒度的、单一职责的 CSS 工具类，用于快速、小范围地调整布局和样式，通常在原型制作或解决特定覆盖问题时使用。

*   **CSS (`utilities.css`):**
    *   提供原子化的、单一职责的工具类（如 `Questioning-mt-sm` 代表 `margin-top: var(--Questioning-spacing-sm);`），用于快速微调布局和样式。
    *   这些工具类应置于较高优先级的 `@layer utilities;`。
    *   **强烈建议谨慎使用工具类**。 过度依赖工具类会削弱语义性，使 HTML 结构变得臃肿，并可能导致样式难以追踪。优先考虑通过语义层或组件层来解决样式问题。工具层更多是作为最后的手段或特定场景的补充。
*   **JavaScript (`utils/*.js`):**
    *   提供与 UI 无直接关联的、纯函数式的辅助工具，如日期格式化、字符串操作、DOM 查询助手、事件总线 (event bus) 、debounce/throttle 函数等。
    *   这些工具应是独立的、可复用的，并且可以按需引入。

## 开发流程设想

1.  **定义设计系统：** 首先，团队（设计师与开发者）共同确定核心设计令牌和通用交互原则。
2.  **配置Questioning基础层：** 将设计令牌转化为CSS变量，配置全局基础样式。
3.  **实现Questioning语义层：**
    *   **这是框架的核心价值所在，也是最具挑战和创造性的部分**。
    *   根据确立的交互原则和通用模式，编写与组件解耦的、基于属性选择器的 CSS 规则 (`semantics.css`)。
    *   开发标准化的 JS 行为模块 (`semantics.js`)，并通过 `data-Questioning-behavior-*` 或语义属性自动附加。
    *   持续测试和迭代语义层的覆盖范围和健壮性。
4.  **开发组件：**
    *   开发者在构建组件时，**首先思考如何利用 HTML 的原生语义和框架语义层提供的规则及行为**来满足大部分样式和行为需求。
    *   为组件编写少量必要的、特定于其结构和独特功能的CSS和JS。
    *   在组件样式中**严格使用基础层定义的 CSS 变量 (设计令牌)**。
    *   确保组件的可访问性，正确使用 ARIA 属性，并与语义层的行为良好集成。
5.  **组装页面与应用：** 
    *   使用开发好的、遵循 Questioning 原则的组件来构建完整的用户界面和应用流程。
    *   受益于框架带来的高度一致性和可预测性。
6.  **迭代与演进：**
    *   在应用开发过程中，当出现新的、具有普遍性的交互模式或样式需求时，**优先考虑将其抽象并沉淀到 Questioning 的语义层或基础层**，而非在多个组件中重复实现或通过工具类临时解决。
    *   鼓励团队成员积极“**质疑**”现有实现，持续优化设计系统和框架本身。

## 我们的目标

Questioning 不仅仅是一个 UI 库或 CSS 框架，它是一种**构建可持续、高品质 Web 应用的方法论和一套支持这种方法论的工具集**。我们旨在赋能团队：
  *   **构建出在视觉、交互和语义上高度和谐统一的应用程序**，提升品牌一致性和用户体验。
  *   **显著降低因设计变更、需求迭代带来的维护成本和风险**，使系统更具韧性。
  *   **编写更具语义、更易于理解和维护的代码**，促进团队协作和知识传承。
  *   **提升开发体验和效率**，让开发者能够更专注于业务逻辑创新，而非重复的基础样式和行为实现。
  *   **天然地构建更具可访问性的应用**，惠及所有用户。
  *   **拥抱变化，促进设计系统与代码实现的良性循环和共同进化**。

---

**[项目状态：概念与早期探索阶段]**

我们深知这是一条需要细致思考和精心打磨的道路，它在某些方面可能与当前主流的纯组件封装思想有所侧重。但我们坚信，对于那些追求极致设计一致性、卓越用户体验和长期可维护性的项目及团队而言，Questioning 所倡导的“系统和谐优先”和“语义驱动”理念具有不可估量的潜力。

如果你对这个理念产生共鸣，或者有任何质疑、建议和创想，我们都热切地欢迎你加入讨论，一同探索、质疑、共建这个可能为 Web 开发带来新视角的项目！