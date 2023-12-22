Automate building and deployment (publishing) of a [[Desktop Application]], [[Web Service Backend]], and [[Web Site or Web Service Frontend]].

### GitHub Actions

`.github/workflows/build.yml` script:

```yaml
name: Make Installers for Public Testing
on:
  push:
    branches:
      - main
      - v*
jobs:
  build:
    name: Build
    strategy:
      matrix:
        os: ['ubuntu', 'windows']
    runs-on: ${{ matrix.os }}-latest
    env:
      build_dir: ./build
      installation_content_dir: ./out
    steps:
      - name: Checkout a repository
        uses: actions/checkout@v2
      - name: Configure a project
        shell: bash  # To allow $VCPKG_INSTALLATION_ROOT on Windows
        env:
          vcpkg_scripts: $VCPKG_INSTALLATION_ROOT/scripts/buildsystems
        run: >
          cmake -S . -B "${{ env.build_dir }}"
          -DCMAKE_BUILD_TYPE=Release
          -DCMAKE_TOOLCHAIN_FILE="${{ env.vcpkg_scripts }}/vcpkg.cmake"
      - name: Build a project
        run: cmake --build "${{ env.build_dir }}" --config Release
      - name: Copy files for distribution
        run: >
          cmake --install "${{ env.build_dir }}"
          --prefix "${{ env.installation_content_dir }}"
          --strip
      - name: Package files for distribution as an artifact
        uses: actions/upload-artifact@v2
        with:
          name: grammar2code-${{ matrix.os }}
          path: ${{ env.installation_content_dir }}
          if-no-files-found: error
```

`.github/workflows/test.yml` script:

```yaml
name: Test
on: [push]
jobs:
  build:
    name: Build
    runs-on: ubuntu-latest
    env:
      build_dir: ./build
      vcpkg_script: $VCPKG_INSTALLATION_ROOT/scripts/buildsystems/vcpkg.cmake
    steps:
      - name: Checkout a repository
        uses: actions/checkout@v2
      - name: Configure a project
        shell: bash  # To allow $VCPKG_INSTALLATION_ROOT on Windows
        run: >
          cmake -S . -B "${{ env.build_dir }}"
          -DCMAKE_BUILD_TYPE=Debug
          -DCMAKE_TOOLCHAIN_FILE="${{ env.vcpkg_script }}"
      - name: Build a project
        run: cmake --build "${{ env.build_dir }}" --target unit_tests
      - name: Run tests
        working-directory: ${{ env.build_dir }}
        run: ctest
```

Building using https://github.com/lukka/run-vcpkg:

```yaml
name: Test Build
on: [push, pull_request]
jobs:
  build:
    name: Build
    runs-on: ubuntu-latest
    env:
      buildDir: build
      testDir: deployment
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Install dependencies with vcpkg
        uses: lukka/run-vcpkg@v7
        with:
          # Installation of packages is handled by CMake, so just install vcpkg
          setupOnly: true
          # Setup a package cache invalidated when vcpkg.json is updated
          appendedCacheKey: ${{ hashFiles( 'vcpkg.json' ) }}
          additionalCachedPaths: ${{ env.buildDir }}/vcpkg_installed
          # The action requires a version of vcpkg, otherwise it aborts
          vcpkgGitCommitId: 8dddc6c899ce6fdbeab38b525a31e7f23cb2d5bb
      - name: Build C/C++ code
        uses: lukka/run-cmake@v3
        with:
          useVcpkgToolchainFile: true
          cmakeBuildType: Debug
          buildDirectory: ${{ env.buildDir }}
          buildWithCMake: true
      - name: Pick files for caching
        run: cmake --install ${{ env.buildDir }} --prefix ${{ env.testDir }}
      - name: Cache build artifacts for other jobs
        uses: actions/cache@v2
        with:
          path: ${{ env.testDir }}
          key: build-${{ runner.os }}-${{ github.sha }}
  unit_test:
    name: Run Unit Tests
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Take test artifacts from a cache
        uses: actions/cache@v2
        with:
          path: .
          key: build-${{ runner.os }}-${{ github.sha }}
      - name: Perform the tests
        run: bin/unit_test_runner
```