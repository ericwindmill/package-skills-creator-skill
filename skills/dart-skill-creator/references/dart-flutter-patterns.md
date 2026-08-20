# Common Dart/Flutter Patterns for Skills

This reference provides examples of how to document common Dart and Flutter patterns in your SKILL.md. Only include patterns that are **actually relevant to your package** — don't copy these verbatim if they don't apply.

## Package Initialization

If your package requires initialization before use (common for packages that manage state, connections, or platform-specific setup):

```markdown
## Initialization

Always initialize the package before use:

```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await MyPackage.initialize(
    config: Config(/* ... */),
  );
  runApp(MyApp());
}
```
```

**When to include this:** Your package has an `initialize()` method or requires setup in `main()`.

## Builder Patterns

If you use builders for configuration (common for HTTP clients, complex objects with many optional parameters):

```markdown
## Configuration

Use the builder pattern for complex configuration:

```dart
final client = MyClient.builder()
  .withTimeout(Duration(seconds: 30))
  .withRetry(maxAttempts: 3)
  .enableLogging()
  .build();
```
```

**When to include this:** Your package provides `.builder()` methods or fluent configuration APIs.

## Dependency Injection (Riverpod, GetIt, etc.)

If your package integrates with DI frameworks or has recommended patterns for DI:

```markdown
## Integration with Riverpod

Define providers for package services:

```dart
final myServiceProvider = Provider((ref) {
  return MyService(
    config: ref.watch(configProvider),
  );
});
```
```

**When to include this:** Your package documentation recommends specific DI patterns, or you provide DI utilities.

## Code Generation

If you use build_runner or code generation (common for JSON serialization, dependency injection, route generation):

```markdown
## Code Generation

After defining models, run code generation:

```bash
dart run build_runner build --delete-conflicting-outputs
```

Import the generated file:

```dart
import 'my_model.g.dart';
```
```

**When to include this:** Your package uses annotations and generates `.g.dart` or `.freezed.dart` files.

## Stream-Based APIs

If your package uses Streams for async data or events:

```markdown
## Listening to Events

Subscribe to the event stream:

```dart
final subscription = myService.eventStream.listen(
  (event) {
    // Handle event
    print('Received: ${event.data}');
  },
  onError: (error) {
    // Handle error
  },
  onDone: () {
    // Stream completed
  },
);

// Don't forget to cancel when done
await subscription.cancel();
```
```

**When to include this:** Your package exposes Streams as primary APIs.

## Testing Utilities

If you provide test helpers or mock implementations:

```markdown
## Testing

Use the provided test utilities:

```dart
import 'package:my_package/testing.dart';

void main() {
  testWidgets('example', (tester) async {
    await tester.pumpWidget(
      MyPackageTestWrapper(
        child: MyWidget(),
      ),
    );

    expect(find.text('Expected'), findsOneWidget);
  });
}
```
```

**When to include this:** Your package exports a `testing.dart` library or provides mock implementations.

## Flutter Widget Patterns

If your package provides Flutter widgets:

```markdown
## Using Widgets

Wrap your app with the configuration widget:

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MyPackageProvider(
      config: MyPackageConfig(...),
      child: MaterialApp(
        home: MyHomePage(),
      ),
    );
  }
}
```

Then use the widgets in your UI:

```dart
MyPackageWidget(
  onData: (data) {
    // Handle data
  },
  child: Text('Content'),
)
```
```

**When to include this:** Your package provides widgets that users compose into their UI.

## Platform Channel / FFI

If your package uses platform channels or FFI for native code:

```markdown
## Platform Requirements

Add platform-specific configuration to your app:

**iOS** - Add to `ios/Runner/Info.plist`:
```xml
<key>NSCameraUsageDescription</key>
<string>We need camera access for...</string>
```

**Android** - Add to `android/app/src/main/AndroidManifest.xml`:
```xml
<uses-permission android:name="android.permission.CAMERA"/>
```
```

**When to include this:** Your package requires platform permissions or native configuration.

## Async/Await Patterns

If your package is heavily async and has specific patterns for handling futures:

```markdown
## Working with Async Operations

All operations return Futures and should be awaited:

```dart
// Good - awaits the result
final result = await client.fetchData();
print(result.value);

// Bad - doesn't await, loses error handling
client.fetchData().then((result) => print(result));
```

Use try-catch for error handling:

```dart
try {
  final result = await client.fetchData();
} on NetworkException catch (e) {
  // Handle network errors
} on TimeoutException catch (e) {
  // Handle timeouts
}
```
```

**When to include this:** You have specific recommendations about async patterns, or common mistakes involve not awaiting.

## Configuration Files

If your package reads configuration from files (YAML, JSON, etc.):

```markdown
## Configuration File

Create a configuration file at the project root:

**pubspec.yaml:**
```yaml
my_package:
  api_key: "your-key"
  endpoint: "https://api.example.com"
  timeout: 30
```

Or use a dedicated config file:

**my_package.yaml:**
```yaml
version: 1
settings:
  cache_enabled: true
  log_level: info
```
```

**When to include this:** Your package reads config from `pubspec.yaml` or dedicated files.

## Tips for Using These Examples

1. **Only include relevant patterns** - If your package doesn't use code generation, don't include that section
2. **Adapt to your API** - These are templates; change class names, parameters, and examples to match your actual API
3. **Show common mistakes** - If users often forget to await, or commonly misconfigure something, add a "Things to Avoid" section
4. **Link to your docs** - If you have detailed setup guides, link to them: "For detailed setup, see [the official docs](https://example.com/docs)"
