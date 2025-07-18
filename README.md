# PHPUnit Code Coverage per Test (Laravel Setup)

This guide describes how to generate **code coverage for specific tests** in a Laravel project and how to **merge coverage reports incrementally**. This is especially helpful for large Laravel applications or CI/CD pipelines.

---

## ✅ Requirements

- **Laravel** 9.x, 10.x, 11.x, or 12.x
- **PHP** 8.1 or higher
- **Xdebug** 3.1+ (must be installed and enabled)
- **PHPUnit** 10+ or 11+
- **phpcov** (install via Composer or PHAR)

> ✅ These tools work the same across all supported Laravel versions. The setup focuses specifically on code coverage merging, not general Laravel configuration.

---

## 🔧 Laravel Configuration

### 1. Enable Xdebug coverage mode

Set the `XDEBUG_MODE` environment variable:

```bash
export XDEBUG_MODE=coverage
```

Or use it inline:

```bash
XDEBUG_MODE=coverage ./vendor/bin/phpunit ...
```

---

### 2. Run a single test with `--coverage-php`

Save raw coverage for a specific test (e.g. `UploadTest`) to a `.cov` file:

```bash
XDEBUG_MODE=coverage ./vendor/bin/phpunit \
  --filter=UploadTest \
  --coverage-php=storage/coverage/uploadTest.cov
```

Repeat for other test classes if needed:

```bash
XDEBUG_MODE=coverage ./vendor/bin/phpunit \
  --filter=UserTest \
  --coverage-php=storage/coverage/userTest.cov
```

> ✅ `.cov` files are not human-readable—they're meant for merging later.

---

### 3. Merge `.cov` files using `phpcov`

Install `phpcov` via Composer:

```bash
composer global require phpunit/phpcov
```

Merge all `.cov` files and generate a combined HTML report:

```bash
phpcov merge storage/coverage/ --html public/coverage
```

You can now open `public/coverage/index.html` in your browser.

---

## 📂 Suggested Structure

```text
your-laravel-app/
│
├── storage/
│   └── coverage/
│       ├── uploadTest.cov
│       ├── userTest.cov
│
├── public/
│   └── coverage/
│       └── index.html
```

---

## ⚙️ Optional: Bash Script for Laravel Coverage

Create a file `coverage.sh`:

```bash
#!/bin/bash

export XDEBUG_MODE=coverage
mkdir -p storage/coverage

# Run Laravel-specific test cases
./vendor/bin/phpunit --filter=UploadTest --coverage-php=storage/coverage/uploadTest.cov
./vendor/bin/phpunit --filter=UserTest --coverage-php=storage/coverage/userTest.cov

# Merge coverage
phpcov merge storage/coverage/ --html public/coverage

echo "✅ Laravel coverage generated in public/coverage"
```

Make it executable:

```bash
chmod +x coverage.sh
```

Run it:

```bash
./coverage.sh
```

---

## 🧪 Optional: Filter Coverage for Specific Laravel Files

You can add specific Laravel classes to your `phpunit.xml`:

```xml
<coverage>
  <include>
    <file>app/Http/Requests/MediaFileRequest.php</file>
    <file>app/Services/MediaFilesService.php</file>
  </include>
</coverage>
```

---

## 🔍 Preview Coverage of a Single File Before Merge

You can generate and inspect coverage for one file or test before merging everything:

### Step 1: Generate temporary coverage HTML

```bash
XDEBUG_MODE=coverage ./vendor/bin/phpunit \
  --filter=UploadTest \
  --coverage-html=storage/coverage_html_single
```

### Step 2: Open the report

Open `storage/coverage_html_single/index.html` in your browser and check if, for example, `MediaFileRequest.php` is 100% green.

### Step 3 (Optional): Restrict to just one file in `phpunit.xml`

```xml
<coverage>
  <include>
    <file>app/Http/Requests/MediaFileRequest.php</file>
  </include>
</coverage>
```

This ensures coverage is calculated **only** for that file during test runs.

---

## 🧠 Tips

- Use `--filter=TestName` to run a specific test class or method.
- `--coverage-html` **overwrites** the report. Use `--coverage-php` + `phpcov` to merge.
- PHPUnit's native HTML reports are helpful, but merging allows **partial regeneration**.
- Use the preview technique to ensure 100% coverage before merging into the full report.
- Works on Laravel 9 through 12+ and any PHP version supported by PHPUnit.

---

## 📎 Resources

- [PHPUnit Docs](https://phpunit.readthedocs.io/)
- [phpcov](https://github.com/sebastianbergmann/phpcov)
- [Xdebug Docs](https://xdebug.org/docs/all_settings#mode)
- [Laravel Testing](https://laravel.com/docs/testing)

---

## 📣 Contribute

Found this helpful? Open a PR or suggest improvements! Great for Laravel teams who want efficient test coverage tracking.

---
