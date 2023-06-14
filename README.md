# fanbname：将 Python 🐍 发行版 📦 发布到 PyP

on: push
jobs:
  tests:
    name: Test package
    runs-on: ubuntu-latest
    strategy:
      matrix:
        # python-version: ['3.7', '3.8', '3.9', '3.10', '3.
        python-version: ['3.8', '3.9', '3.10', '3.11']
    steps:
    - uses: actions/checkout@master
    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v4
      with:
        python-version: ${{ matrix.python-version }}
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        python -m pip install tox tox-gh-actions
    - name: Test with tox
      run: tox
  build-n-publish:
    name: Build and publish Python 🐍 distributions 📦 to PyPI and TestPyPI
    runs-on: ubuntu-latest
    needs: [tests]
    steps:
    - uses: actions/checkout@master
    - name: Set up Python 3.11
      uses: actions/setup-python@v3
      with:
        python-version: "3.11"
    - name: Install pypa/build
      run: >-
        python -m
        pip install
        build
        --user
    - name: Build a binary wheel and a source tarball
      run: >-
        python -m
        build
        --sdist
        --wheel
        --outdir dist/
    - name: Publish distribution 📦 to PyPI
      if: startsWith(github.ref, 'refs/tags')
      uses: pypa/gh-action-pypi-publish@release/v1
      with:
        password: ${{ secrets.PYPI_API_TOKEN }}
