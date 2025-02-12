## Version 5.2 Updated
CXX = g++
CXXFLAGS = -c -Wall
TARGET = hello
SRC = $(wildcard *.cpp)
# patsubst will substitute ".cpp" to ".o" for all files in $(SRC)
OBJ = $(patsubst %.cpp, %.o, $(SRC))

$(TARGET): $(OBJ)
	$(CXX) -o $@ $^

makefile.dependencies: $(SRC)
	$(CXX) $(CXXFLAGS) -MM $(SRC) > makefile.dependencies

include makefile.dependencies

.PHONY: clean
clean:
	rm -f *.o makefile.dependencies $(TARGET)