## Version 4
CXX = g++
CXXFLAGS = -c -Wall
TARGET = hello
SRC = $(wildcard *.cpp) 
#wildcard function will search all the matched files and stored their name
OBJ = $(patsubst %.cpp, %.o, $(SRC))
#patsubst will substitude ".cpp" to ".o"  for all files in $(SRC)

$(TARGET): $(OBJ)
	$(CXX) -o $@ $^
# $@ stand for target
# $^ stand for objects

%.o: %.cpp
	$(CXX) $(CXXFLAGS) $< -o $@
# $< stand for the frist objects

.PHONY: clean
clean:
	rm -f *.o $(TARGET)