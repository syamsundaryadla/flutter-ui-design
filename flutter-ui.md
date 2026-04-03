# Flutter UI/UX Design Guidelines
### Premium App Standards · Apple HIG–Inspired · AI-Agent Ready

---

## Table of Contents

1. [Design Philosophy](#1-design-philosophy)
2. [Layout Structure & Spacing](#2-layout-structure--spacing)
3. [Typography](#3-typography)
4. [Color System](#4-color-system)
5. [Components](#5-components)
6. [Animations & Transitions](#6-animations--transitions)
7. [Iconography & Imagery](#7-iconography--imagery)
8. [Responsiveness](#8-responsiveness-across-devices)
9. [Accessibility](#9-accessibility-considerations)
10. [Do's and Don'ts](#10-dos-and-donts)

---

## 1. Design Philosophy

### Core Principles

The UI must feel **intentional, calm, and effortless**. Every pixel serves a purpose. Inspired by Apple's Human Interface Guidelines, the design language should communicate trust, clarity, and delight without noise.

| Principle | Description |
|-----------|-------------|
| **Clarity** | Text is legible, icons are precise, affordances are obvious |
| **Deference** | The UI defers to content — chrome never competes with data |
| **Depth** | Subtle layering, shadows, and motion create spatial hierarchy |
| **Restraint** | Add nothing unless it improves comprehension or usability |
| **Consistency** | Every screen obeys the same visual grammar |

### Voice of the Interface
- Clean, not bare
- Modern, not trendy
- Helpful, not intrusive
- Premium, not flashy

### Flutter Implementation Note
Use `ThemeData` at the app root to enforce consistency. Never hardcode colors, fonts, or spacing inline — always reference theme tokens or design constants defined in a central `app_theme.dart` file.

```dart
// ✅ Correct
Text('Hello', style: Theme.of(context).textTheme.titleLarge)

// ❌ Wrong
Text('Hello', style: TextStyle(fontSize: 20, color: Colors.black))
```

---

## 2. Layout Structure & Spacing

### Spacing Scale

Use an **8pt base grid**. All spacing values must be multiples of 4 or 8.

```dart
// Define in a constants file: lib/core/constants/spacing.dart
class AppSpacing {
  static const double xs   =  4.0;
  static const double sm   =  8.0;
  static const double md   = 16.0;
  static const double lg   = 24.0;
  static const double xl   = 32.0;
  static const double xxl  = 48.0;
  static const double xxxl = 64.0;
}
```

### Page Layout

```
┌──────────────────────────────────┐
│  Status Bar                      │
├──────────────────────────────────┤
│  Navigation Bar (56–64dp)        │
├──────────────────────────────────┤
│                                  │
│   Content Area                   │
│   Horizontal padding: 16–24dp    │
│   Max content width: 600dp       │
│                                  │
├──────────────────────────────────┤
│  Bottom Navigation / Tab Bar     │
│  Safe Area inset respected       │
└──────────────────────────────────┘
```

### Padding & Margin Rules

- **Screen edge padding**: `16dp` on phones, `24dp` on tablets
- **Card internal padding**: `16dp` all sides
- **Section spacing**: `24dp` between major sections
- **Element spacing**: `8–12dp` between sibling elements
- **List item height**: minimum `56dp`, comfortable `64dp`
- **Touch target**: minimum `44×44dp` (Apple HIG standard)

### Flutter Implementation

```dart
// Page scaffold template
Scaffold(
  body: SafeArea(
    child: Padding(
      padding: const EdgeInsets.symmetric(horizontal: AppSpacing.md),
      child: /* content */,
    ),
  ),
)

// Section spacing
Column(
  children: [
    SectionWidget(),
    const SizedBox(height: AppSpacing.lg), // 24dp between sections
    SectionWidget(),
  ],
)
```

### Grid System

- Use `GridView` with `crossAxisSpacing: 12` and `mainAxisSpacing: 12`
- Card grids: 2 columns on phone, 3–4 on tablet
- Always use `SliverGrid` inside `CustomScrollView` for scrollable grids

---

## 3. Typography

### Font Choice

**Primary font**: Use `SF Pro` on iOS feel–driven apps, or `Inter` / `Plus Jakarta Sans` as cross-platform alternatives. Load via `google_fonts` package.

```dart
// pubspec.yaml
dependencies:
  google_fonts: ^6.x.x
```

```dart
// app_theme.dart
import 'package:google_fonts/google_fonts.dart';

TextTheme _buildTextTheme(TextTheme base) {
  return GoogleFonts.interTextTheme(base);
}
```

### Type Scale

| Role | Flutter Style | Size | Weight | Line Height | Use Case |
|------|--------------|------|--------|-------------|----------|
| `displayLarge` | — | 57sp | 400 | 64dp | Hero headlines |
| `displayMedium` | — | 45sp | 400 | 52dp | Feature titles |
| `headlineLarge` | — | 32sp | 600 | 40dp | Page titles |
| `headlineMedium` | — | 28sp | 600 | 36dp | Section headers |
| `titleLarge` | — | 22sp | 600 | 28dp | Card titles, nav headers |
| `titleMedium` | — | 16sp | 500 | 24dp | List item titles |
| `titleSmall` | — | 14sp | 500 | 20dp | Sub-labels |
| `bodyLarge` | — | 16sp | 400 | 24dp | Primary body content |
| `bodyMedium` | — | 14sp | 400 | 20dp | Secondary content |
| `bodySmall` | — | 12sp | 400 | 16dp | Captions, metadata |
| `labelLarge` | — | 14sp | 600 | 20dp | Buttons |
| `labelSmall` | — | 11sp | 500 | 16dp | Tags, badges |

### Typography Rules

- **Never** use more than 3 type sizes on a single screen
- **Line length**: 50–75 characters for body text (use `maxWidth` constraints)
- **Letter spacing**: Slightly negative (−0.2 to −0.5) for headlines; 0 to +0.1 for body
- **Never** use pure black (`#000000`) for text — use `#1C1C1E` (iOS dark) or `#111827`
- **Hierarchy**: Create contrast via weight, not size alone

```dart
// Example: Proper hierarchy
Column(
  crossAxisAlignment: CrossAxisAlignment.start,
  children: [
    Text('Good Morning', style: theme.textTheme.titleSmall?.copyWith(
      color: theme.colorScheme.primary,
      letterSpacing: 0.5,
    )),
    const SizedBox(height: 4),
    Text('Your Dashboard', style: theme.textTheme.headlineMedium),
    const SizedBox(height: 8),
    Text('Here\'s what\'s happening today.', style: theme.textTheme.bodyMedium?.copyWith(
      color: theme.colorScheme.onSurface.withOpacity(0.6),
    )),
  ],
)
```

---

## 4. Color System

### Palette Architecture

Define all colors as semantic tokens, never raw hex values in widgets.

```dart
// lib/core/theme/app_colors.dart

class AppColors {
  // Light Mode
  static const Color primaryLight      = Color(0xFF007AFF); // iOS Blue
  static const Color backgroundLight   = Color(0xFFF2F2F7); // iOS System BG
  static const Color surfaceLight      = Color(0xFFFFFFFF);
  static const Color onSurfaceLight    = Color(0xFF1C1C1E);
  static const Color subtleLight       = Color(0xFF8E8E93); // Secondary label
  static const Color dividerLight      = Color(0xFFE5E5EA);
  static const Color errorLight        = Color(0xFFFF3B30);
  static const Color successLight      = Color(0xFF34C759);
  static const Color warningLight      = Color(0xFFFF9F0A);

  // Dark Mode
  static const Color primaryDark       = Color(0xFF0A84FF);
  static const Color backgroundDark    = Color(0xFF000000);
  static const Color surfaceDark       = Color(0xFF1C1C1E);
  static const Color onSurfaceDark     = Color(0xFFFFFFFF);
  static const Color subtleDark        = Color(0xFF8E8E93);
  static const Color dividerDark       = Color(0xFF38383A);
  static const Color errorDark         = Color(0xFFFF453A);
  static const Color successDark       = Color(0xFF30D158);
  static const Color warningDark       = Color(0xFFFFD60A);
}
```

### ColorScheme Integration

```dart
ColorScheme lightColorScheme = ColorScheme(
  brightness: Brightness.light,
  primary:        AppColors.primaryLight,
  onPrimary:      Colors.white,
  secondary:      AppColors.primaryLight.withOpacity(0.1),
  onSecondary:    AppColors.primaryLight,
  background:     AppColors.backgroundLight,
  onBackground:   AppColors.onSurfaceLight,
  surface:        AppColors.surfaceLight,
  onSurface:      AppColors.onSurfaceLight,
  error:          AppColors.errorLight,
  onError:        Colors.white,
);
```

### Layering System (Elevation via Color, Not Shadow)

Avoid heavy drop shadows. Use subtle color elevation as Apple does:

| Layer | Light BG | Dark BG | Usage |
|-------|----------|---------|-------|
| Background | `#F2F2F7` | `#000000` | Screen base |
| Surface L1 | `#FFFFFF` | `#1C1C1E` | Cards, sheets |
| Surface L2 | `#F2F2F7` | `#2C2C2E` | Nested cards |
| Surface L3 | `#E5E5EA` | `#3A3A3C` | Modals, overlays |

### Glassmorphism (Use Sparingly)

```dart
// For navigation bars or floating elements
ClipRRect(
  borderRadius: BorderRadius.circular(16),
  child: BackdropFilter(
    filter: ImageFilter.blur(sigmaX: 20, sigmaY: 20),
    child: Container(
      color: Colors.white.withOpacity(0.7),
      child: /* content */,
    ),
  ),
)
```

### Contrast Requirements

- Body text on background: **minimum 4.5:1** (WCAG AA)
- Large text / UI elements: **minimum 3:1**
- Use `ThemeData.estimateBrightnessForColor` to auto-select text color on dynamic backgrounds

---

## 5. Components

### 5.1 Buttons

#### Primary Button
```dart
FilledButton(
  onPressed: onTap,
  style: FilledButton.styleFrom(
    minimumSize: const Size(double.infinity, 52),
    shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(14)),
    textStyle: theme.textTheme.labelLarge,
  ),
  child: const Text('Continue'),
)
```

#### Secondary / Outline Button
```dart
OutlinedButton(
  onPressed: onTap,
  style: OutlinedButton.styleFrom(
    minimumSize: const Size(double.infinity, 52),
    shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(14)),
    side: BorderSide(color: theme.colorScheme.primary, width: 1.5),
  ),
  child: const Text('Learn More'),
)
```

#### Ghost / Text Button
```dart
TextButton(
  onPressed: onTap,
  child: Text('Skip for now', style: TextStyle(
    color: theme.colorScheme.onSurface.withOpacity(0.5),
  )),
)
```

#### Button Rules
- Full-width primary CTA: use `double.infinity` width
- Rounded corners: `12–16dp` radius
- Height: `48–56dp`
- Loading state: Replace text with `SizedBox(width: 20, height: 20, child: CircularProgressIndicator(strokeWidth: 2))`
- **Never** use flat rectangular buttons — always round corners

---

### 5.2 Cards

```dart
Container(
  decoration: BoxDecoration(
    color: theme.colorScheme.surface,
    borderRadius: BorderRadius.circular(16),
    boxShadow: [
      BoxShadow(
        color: Colors.black.withOpacity(0.04),
        blurRadius: 12,
        offset: const Offset(0, 4),
      ),
      BoxShadow(
        color: Colors.black.withOpacity(0.02),
        blurRadius: 4,
        offset: const Offset(0, 1),
      ),
    ],
  ),
  padding: const EdgeInsets.all(AppSpacing.md),
  child: /* card content */,
)
```

#### Card Rules
- Border radius: `12–20dp`
- Shadow: **very subtle** (opacity 0.04–0.08), never heavy
- In dark mode: use `Surface L1` background color instead of shadows
- Avoid `Card` widget directly — it adds unwanted elevation artifacts; use `Container` with `BoxDecoration`

---

### 5.3 Text Inputs

```dart
TextField(
  decoration: InputDecoration(
    labelText: 'Email address',
    hintText: 'you@example.com',
    filled: true,
    fillColor: theme.colorScheme.surface,
    contentPadding: const EdgeInsets.symmetric(horizontal: 16, vertical: 14),
    border: OutlineInputBorder(
      borderRadius: BorderRadius.circular(12),
      borderSide: BorderSide(color: AppColors.dividerLight, width: 1),
    ),
    enabledBorder: OutlineInputBorder(
      borderRadius: BorderRadius.circular(12),
      borderSide: BorderSide(color: AppColors.dividerLight, width: 1),
    ),
    focusedBorder: OutlineInputBorder(
      borderRadius: BorderRadius.circular(12),
      borderSide: BorderSide(color: theme.colorScheme.primary, width: 1.5),
    ),
    errorBorder: OutlineInputBorder(
      borderRadius: BorderRadius.circular(12),
      borderSide: BorderSide(color: theme.colorScheme.error, width: 1.5),
    ),
    prefixIcon: Icon(CupertinoIcons.mail, size: 20),
  ),
)
```

#### Input Rules
- Label animates above on focus (`labelText`, not `hintText` alone)
- Error messages appear below in `bodySmall` red
- Height: `52–56dp` for comfortable touch
- Avoid underline-only inputs — use `OutlineInputBorder` with `filled: true`

---

### 5.4 Navigation Bar

#### Bottom Navigation Bar
```dart
NavigationBar(
  height: 64,
  indicatorColor: theme.colorScheme.primary.withOpacity(0.12),
  labelBehavior: NavigationDestinationLabelBehavior.alwaysShow,
  destinations: const [
    NavigationDestination(icon: Icon(CupertinoIcons.home), label: 'Home'),
    NavigationDestination(icon: Icon(CupertinoIcons.search), label: 'Search'),
    NavigationDestination(icon: Icon(CupertinoIcons.person), label: 'Profile'),
  ],
)
```

#### App Bar
```dart
AppBar(
  scrolledUnderElevation: 0.5,  // subtle separator on scroll
  backgroundColor: theme.colorScheme.background,
  surfaceTintColor: Colors.transparent,
  title: Text('Page Title', style: theme.textTheme.titleLarge),
  centerTitle: false,           // left-align on Android, center on iOS feel
  actions: [
    IconButton(icon: const Icon(CupertinoIcons.bell), onPressed: () {}),
  ],
)
```

---

### 5.5 Modals & Bottom Sheets

```dart
showModalBottomSheet(
  context: context,
  isScrollControlled: true,
  backgroundColor: Colors.transparent,
  builder: (context) => DraggableScrollableSheet(
    initialChildSize: 0.6,
    minChildSize: 0.4,
    maxChildSize: 0.95,
    builder: (_, controller) => Container(
      decoration: BoxDecoration(
        color: theme.colorScheme.surface,
        borderRadius: const BorderRadius.vertical(top: Radius.circular(24)),
      ),
      child: Column(
        children: [
          // Drag handle
          Container(
            margin: const EdgeInsets.only(top: 12, bottom: 8),
            width: 36, height: 4,
            decoration: BoxDecoration(
              color: theme.colorScheme.onSurface.withOpacity(0.2),
              borderRadius: BorderRadius.circular(2),
            ),
          ),
          Expanded(child: ListView(controller: controller, children: [/* content */])),
        ],
      ),
    ),
  ),
);
```

---

### 5.6 List Items

```dart
// Premium list tile
InkWell(
  onTap: onTap,
  borderRadius: BorderRadius.circular(12),
  child: Padding(
    padding: const EdgeInsets.symmetric(horizontal: 16, vertical: 12),
    child: Row(
      children: [
        // Leading icon container
        Container(
          width: 40, height: 40,
          decoration: BoxDecoration(
            color: theme.colorScheme.primary.withOpacity(0.1),
            borderRadius: BorderRadius.circular(10),
          ),
          child: Icon(icon, size: 20, color: theme.colorScheme.primary),
        ),
        const SizedBox(width: 12),
        Expanded(
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              Text(title, style: theme.textTheme.titleMedium),
              if (subtitle != null)
                Text(subtitle!, style: theme.textTheme.bodySmall?.copyWith(
                  color: theme.colorScheme.onSurface.withOpacity(0.5),
                )),
            ],
          ),
        ),
        Icon(CupertinoIcons.chevron_right, size: 16,
          color: theme.colorScheme.onSurface.withOpacity(0.3)),
      ],
    ),
  ),
)
```

---

### 5.7 Tags & Badges

```dart
// Status chip
Container(
  padding: const EdgeInsets.symmetric(horizontal: 10, vertical: 4),
  decoration: BoxDecoration(
    color: AppColors.successLight.withOpacity(0.12),
    borderRadius: BorderRadius.circular(20),
  ),
  child: Text('Active', style: theme.textTheme.labelSmall?.copyWith(
    color: AppColors.successLight,
    fontWeight: FontWeight.w600,
  )),
)
```

---

## 6. Animations & Transitions

### Core Principles
- Animations should **convey continuity**, not show off
- Duration: **150–350ms** for UI transitions (never exceed 500ms)
- Easing: always use **curves**, never linear
- Prefer **implicit animations** (`AnimatedContainer`, `AnimatedOpacity`) for simple state changes

### Recommended Curves

```dart
// Use these curves exclusively
Curves.easeOut        // Most UI transitions (elements appearing)
Curves.easeIn         // Elements dismissing
Curves.easeInOut      // Bidirectional (size, position changes)
Curves.fastOutSlowIn  // Material standard — slides, navigation
Curves.decelerate     // Content arriving from off-screen
```

### Standard Durations

```dart
class AppDuration {
  static const Duration instant  = Duration(milliseconds: 100);
  static const Duration fast     = Duration(milliseconds: 150);
  static const Duration normal   = Duration(milliseconds: 250);
  static const Duration slow     = Duration(milliseconds: 350);
  static const Duration page     = Duration(milliseconds: 300);
}
```

### Page Transitions

```dart
// Smooth fade + slide (iOS-style)
PageRouteBuilder(
  transitionDuration: AppDuration.page,
  pageBuilder: (_, __, ___) => const NextPage(),
  transitionsBuilder: (_, animation, __, child) {
    final curved = CurvedAnimation(parent: animation, curve: Curves.fastOutSlowIn);
    return FadeTransition(
      opacity: curved,
      child: SlideTransition(
        position: Tween<Offset>(begin: const Offset(0.05, 0), end: Offset.zero)
          .animate(curved),
        child: child,
      ),
    );
  },
)
```

### Micro-interactions

```dart
// Press feedback — scale down on tap
GestureDetector(
  onTapDown: (_) => setState(() => _scale = 0.97),
  onTapUp: (_) => setState(() => _scale = 1.0),
  onTapCancel: () => setState(() => _scale = 1.0),
  child: AnimatedScale(
    scale: _scale,
    duration: AppDuration.fast,
    curve: Curves.easeOut,
    child: /* button or card */,
  ),
)
```

### Staggered List Animations

```dart
// Animate list items in sequence
AnimationConfiguration.staggeredList(
  position: index,
  duration: const Duration(milliseconds: 300),
  child: SlideAnimation(
    verticalOffset: 20,
    child: FadeInAnimation(child: ListItemWidget()),
  ),
)
// Use flutter_staggered_animations package
```

### Skeleton Loading (Not Spinners)

Use shimmer placeholders for loading states instead of centered spinners.

```dart
// Use shimmer package
Shimmer.fromColors(
  baseColor: theme.colorScheme.surface,
  highlightColor: theme.colorScheme.onSurface.withOpacity(0.05),
  child: SkeletonCardWidget(), // mirrors real card layout
)
```

---

## 7. Iconography & Imagery

### Icon System

**Primary icon set**: `cupertino_icons` for iOS feel, or `lucide_flutter` for a clean cross-platform look.

```dart
// pubspec.yaml
dependencies:
  cupertino_icons: ^1.0.x
  lucide_icons: ^0.x.x   # optional modern alternative
```

### Icon Rules

| Rule | Spec |
|------|------|
| Standard icon size | `20–24dp` |
| Navigation bar icons | `24dp` |
| Inline text icons | `16–18dp` |
| Action/hero icons | `28–32dp` |
| Style | Outlined (not filled) for inactive; filled for active state |
| Color | Match contextual text color or `colorScheme.primary` |

```dart
// ✅ Icon with semantic color
Icon(
  CupertinoIcons.bell_fill,
  size: 22,
  color: theme.colorScheme.primary,
)

// ❌ Never hardcode icon colors
Icon(CupertinoIcons.bell_fill, color: Colors.blue)
```

### App Icon Style
- Use rounded square containers (squircle, `borderRadius: 10–12`) for feature icons
- Background: brand color at 10–15% opacity; icon in full brand color
- Consistent 40×40 container size across list items

### Imagery Guidelines

- Images must use `BorderRadius.circular(12–16)` clipping
- Always provide `fit: BoxFit.cover` for aspect-ratio-constrained containers
- Use `CachedNetworkImage` for all remote images (with placeholder and error widgets)
- Hero images: 16:9 or 4:3 aspect ratio; avatar images: 1:1

```dart
ClipRRect(
  borderRadius: BorderRadius.circular(14),
  child: CachedNetworkImage(
    imageUrl: url,
    fit: BoxFit.cover,
    placeholder: (_, __) => ShimmerPlaceholder(),
    errorWidget: (_, __, ___) => Container(
      color: theme.colorScheme.surface,
      child: Icon(CupertinoIcons.photo, color: theme.colorScheme.onSurface.withOpacity(0.3)),
    ),
  ),
)
```

---

## 8. Responsiveness Across Devices

### Breakpoints

```dart
class AppBreakpoints {
  static const double mobile  = 0;
  static const double tablet  = 600;
  static const double desktop = 1024;
}

// Usage helper
extension ResponsiveContext on BuildContext {
  bool get isMobile  => MediaQuery.of(this).size.width < 600;
  bool get isTablet  => MediaQuery.of(this).size.width >= 600;
  bool get isDesktop => MediaQuery.of(this).size.width >= 1024;
  double get screenWidth => MediaQuery.of(this).size.width;
}
```

### Responsive Layout Pattern

```dart
LayoutBuilder(
  builder: (context, constraints) {
    if (constraints.maxWidth >= 600) {
      // Tablet: 2-column layout
      return Row(
        children: [
          SidebarWidget(),
          Expanded(child: ContentWidget()),
        ],
      );
    }
    // Phone: single column
    return ContentWidget();
  },
)
```

### Device-Specific Adjustments

| Property | Phone | Tablet |
|----------|-------|--------|
| Horizontal padding | 16dp | 24–32dp |
| Max content width | 100% | 680dp (centered) |
| Grid columns | 2 | 3–4 |
| Font scale | 1.0x | 1.0–1.1x |
| Navigation | Bottom bar | Side rail |
| Card layout | Full width list | Grid |

### Centering Content on Wide Screens

```dart
Center(
  child: ConstrainedBox(
    constraints: const BoxConstraints(maxWidth: 680),
    child: /* page content */,
  ),
)
```

### Safe Areas

Always respect safe areas. **Never** place interactive elements under notch or home indicator.

```dart
// Always wrap screen content
Scaffold(
  body: SafeArea(child: /* content */),
)

// For custom bottom bars
Padding(
  padding: EdgeInsets.only(bottom: MediaQuery.of(context).padding.bottom + 8),
  child: BottomBar(),
)
```

---

## 9. Accessibility Considerations

### Touch Targets

Every tappable element must be **at least 44×44dp**.

```dart
// For small icons, wrap with SizedBox to enforce minimum target
SizedBox(
  width: 44, height: 44,
  child: IconButton(
    icon: const Icon(CupertinoIcons.xmark, size: 18),
    onPressed: onClose,
  ),
)
```

### Semantic Labels

```dart
Semantics(
  label: 'Close dialog',
  button: true,
  child: IconButton(icon: const Icon(CupertinoIcons.xmark), onPressed: onClose),
)

// For images
Semantics(
  label: 'Profile photo of ${user.name}',
  child: CircleAvatar(backgroundImage: NetworkImage(user.avatarUrl)),
)
```

### Text Scaling

Support Dynamic Type / system font scaling. Never block text scaling.

```dart
// ✅ Allow scaling
Text('Hello', style: theme.textTheme.bodyLarge)

// ❌ Never block
Text('Hello', textScaleFactor: 1.0) // blocks accessibility
```

Constrain only when layout would break, using `maxLines` + `overflow`:

```dart
Text(
  title,
  style: theme.textTheme.titleMedium,
  maxLines: 2,
  overflow: TextOverflow.ellipsis,
)
```

### Color & Contrast Checklist

- [ ] All body text achieves **4.5:1** contrast on its background
- [ ] All interactive element states (focus, hover, disabled) are visually distinct
- [ ] No information is conveyed by **color alone** — pair with icon or label
- [ ] Disabled state uses `onSurface.withOpacity(0.38)` — never invisible

### Focus & Keyboard Navigation

```dart
// Use FocusNode to manage keyboard order
FocusTraversalGroup(
  policy: OrderedTraversalPolicy(),
  child: Column(children: [
    FocusTraversalOrder(order: const NumericFocusOrder(1), child: EmailField()),
    FocusTraversalOrder(order: const NumericFocusOrder(2), child: PasswordField()),
    FocusTraversalOrder(order: const NumericFocusOrder(3), child: SubmitButton()),
  ]),
)
```

### Motion Sensitivity

Respect `disableAnimations` setting:

```dart
bool reduceMotion = MediaQuery.of(context).disableAnimations;

AnimatedContainer(
  duration: reduceMotion ? Duration.zero : AppDuration.normal,
  // ...
)
```

---

## 10. Do's and Don'ts

### ✅ DO

| Do | Reason |
|----|--------|
| Use 8pt spacing grid consistently | Creates visual rhythm and harmony |
| Define all colors/fonts as theme tokens | Enables dark mode and theming without refactor |
| Use `SafeArea` on every screen | Prevents content under notch/home bar |
| Add `Hero` animations for shared elements | Creates spatial continuity between screens |
| Use `Semantics` widgets on icons and images | Screen reader accessibility |
| Show skeleton loaders instead of spinners | Premium loading feel; prevents layout shift |
| Use `CachedNetworkImage` for all remote images | Performance + offline graceful degradation |
| Animate state changes with `AnimatedSwitcher` | Smooth visual state feedback |
| Constrain max content width on tablets | Prevents uncomfortable line lengths |
| Test both light and dark mode after every change | Dark mode parity is expected, not optional |
| Use `CupertinoIcons` or `Lucide` consistently | Visual consistency throughout the app |
| Provide haptic feedback on important actions | `HapticFeedback.lightImpact()` for taps |

### ❌ DON'T

| Don't | Why Not |
|-------|---------|
| Hardcode hex colors in widget files | Breaks theming; unmaintainable at scale |
| Use `Colors.black` or `Colors.white` directly | Fails in dark mode; use `colorScheme` tokens |
| Use more than 2 font weights per screen | Creates visual noise |
| Use heavy drop shadows (opacity > 0.15) | Looks dated; violates flat-layering design |
| Use the `Card` widget without customizing it | Default Material elevation looks cheap |
| Place tap handlers on elements smaller than 44dp | Fails accessibility; frustrating UX |
| Use `MediaQuery.of(context).size` in deep widget trees | Causes unnecessary rebuilds; use `LayoutBuilder` |
| Display raw loading spinners in the center of screens | Poor perceived performance |
| Use `Spacer()` between every element | Breaks on different screen sizes; use fixed spacing |
| Mix icon styles (outlined + filled + rounded) | Visual inconsistency |
| Use linear animation curves | Feels mechanical; always use easing curves |
| Navigate with `pushReplacement` when back is expected | Breaks user mental model |
| Use `showDialog` for confirmations without a cancel option | Violates user control and freedom |
| Display error states without a retry action | Dead ends frustrate users |
| Use `Text` inside `Row` without `Expanded` or `Flexible` | Causes overflow on smaller screens |

---

## Appendix: Theme Setup Template

```dart
// lib/core/theme/app_theme.dart

import 'package:flutter/material.dart';
import 'package:flutter/cupertino.dart';
import 'package:google_fonts/google_fonts.dart';
import 'app_colors.dart';

class AppTheme {
  static ThemeData light() {
    final base = ThemeData(
      useMaterial3: true,
      brightness: Brightness.light,
      colorScheme: _lightColorScheme,
      textTheme: GoogleFonts.interTextTheme(ThemeData.light().textTheme),
      scaffoldBackgroundColor: AppColors.backgroundLight,
      appBarTheme: const AppBarTheme(
        elevation: 0,
        scrolledUnderElevation: 0.5,
        backgroundColor: AppColors.backgroundLight,
        surfaceTintColor: Colors.transparent,
        titleTextStyle: TextStyle(
          fontFamily: 'Inter',
          fontSize: 18,
          fontWeight: FontWeight.w600,
          color: AppColors.onSurfaceLight,
        ),
      ),
      inputDecorationTheme: InputDecorationTheme(
        filled: true,
        fillColor: AppColors.surfaceLight,
        contentPadding: const EdgeInsets.symmetric(horizontal: 16, vertical: 14),
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(12),
          borderSide: const BorderSide(color: AppColors.dividerLight),
        ),
        enabledBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(12),
          borderSide: const BorderSide(color: AppColors.dividerLight),
        ),
        focusedBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(12),
          borderSide: const BorderSide(color: AppColors.primaryLight, width: 1.5),
        ),
      ),
      filledButtonTheme: FilledButtonThemeData(
        style: FilledButton.styleFrom(
          minimumSize: const Size(double.infinity, 52),
          shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(14)),
        ),
      ),
      navigationBarTheme: NavigationBarThemeData(
        height: 64,
        backgroundColor: AppColors.surfaceLight,
        indicatorColor: AppColors.primaryLight.withOpacity(0.12),
        labelTextStyle: MaterialStateProperty.all(
          const TextStyle(fontSize: 11, fontWeight: FontWeight.w500),
        ),
      ),
    );
    return base;
  }

  static ThemeData dark() {
    return light().copyWith(
      brightness: Brightness.dark,
      colorScheme: _darkColorScheme,
      scaffoldBackgroundColor: AppColors.backgroundDark,
      textTheme: GoogleFonts.interTextTheme(ThemeData.dark().textTheme),
    );
  }

  static final _lightColorScheme = ColorScheme(
    brightness: Brightness.light,
    primary: AppColors.primaryLight,
    onPrimary: Colors.white,
    secondary: AppColors.primaryLight.withOpacity(0.1),
    onSecondary: AppColors.primaryLight,
    background: AppColors.backgroundLight,
    onBackground: AppColors.onSurfaceLight,
    surface: AppColors.surfaceLight,
    onSurface: AppColors.onSurfaceLight,
    error: AppColors.errorLight,
    onError: Colors.white,
  );

  static final _darkColorScheme = ColorScheme(
    brightness: Brightness.dark,
    primary: AppColors.primaryDark,
    onPrimary: Colors.white,
    secondary: AppColors.primaryDark.withOpacity(0.15),
    onSecondary: AppColors.primaryDark,
    background: AppColors.backgroundDark,
    onBackground: AppColors.onSurfaceDark,
    surface: AppColors.surfaceDark,
    onSurface: AppColors.onSurfaceDark,
    error: AppColors.errorDark,
    onError: Colors.white,
  );
}
```

```dart
// main.dart
MaterialApp(
  theme: AppTheme.light(),
  darkTheme: AppTheme.dark(),
  themeMode: ThemeMode.system, // respects OS preference
  home: const RootPage(),
)
```

---

*Last updated: 2025 · Follows Material 3 + Apple HIG principles · Flutter 3.x+*
