# Patched qtermwidget bindings for miqt

These bindings are for macOS, fixed to compile and run with miqt miqt-qtermwidget branch of git and qt5

```
cd <yourproject>
go get github.com/mappu/miqt/qt-restricted-extras/qtermwidget@miqt-qtermwidget
cd ~/go/pkg/mod/github.com/mappu/miqt@v0.9.1-0.20250412232338-3d0e923e3299/
rm -rf qtermwidget
git clone https://github.com/e1z0/miqt-qtermwidget.git qtermwidget
```

# Libs

You also have to install qtermwidget library on top of qt5

```
brew install qt@5 cmake ninja git
git clone https://github.com/lxqt/qtermwidget.git
cd qtermwidget
git checkout 58981da
export QT_PLUGIN_PATH="$(brew --prefix qt@5)/plugins"
export DYLD_FRAMEWORK_PATH="$(brew --prefix qt@5)/lib"
mkdir build && cd build
cmake .. -GNinja \
  -DCMAKE_INSTALL_PREFIX=/usr/local \
  -DCMAKE_PREFIX_PATH="$(brew --prefix qt@5)" \
  -DUSE_QT5=true
ninja
ninja install # ninja uninstall
cd ..
cd utf8proc-2.10.0
mkdir build && cd build && cmake ..
make
sudo make install

```

# Build your project


```
CGO_ENABLED=1 \
PATH="$(brew --prefix qt@5)/bin:$$PATH" \
PKG_CONFIG_PATH="$(brew --prefix qt@5)/lib/pkgconfig:${PWD}/pkgconfig" \
CGO_CXXFLAGS="-I$(brew --prefix qt@5)/include -I/usr/local/include -I/usr/local/include/qtermwidget5" \
CGO_LDFLAGS="-L$(brew --prefix qt@5)/lib" \
go build -ldflags '-v -s -w' -o main main.go
```

# MacOS app bundle

```
rm -rf release/miqt-term.app
cp -r skeleton release/miqt-term.app
cp main release/miqt-term.app/Contents/MacOS/main
chmod +x release/miqt-term.app/Contents/MacOS/main
macdeployqt release/miqt-term.app -verbose=1 -always-overwrite -executable=release/miqt-term.app/Contents/MacOS/main
codesign --force --deep --sign - release/miqt-term.app
touch release/miqt-term.app
```
