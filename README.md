# EigenVersionProblem

Code used to demonstrate an issue in the discussion:

<https://github.com/microsoft/onnxruntime/issues/2812>

Should be possible to compile with any reasonable C++ compiler,
and a recent CMake version.

The expected output after compilation is:

```sh
[bash][thor]:install > ./bin/testNew
New printout
[bash][thor]:install > ./bin/testOld
Old printout
[bash][thor]:install > ./bin/testBoth1
Old printout
Old printout
[bash][thor]:install > ./bin/testBoth2
New printout
New printout
[bash][thor]:install >
```
