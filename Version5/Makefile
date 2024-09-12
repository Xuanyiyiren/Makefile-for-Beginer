## Version 5
CXX = g++
CXXFLAGS = -c -Wall
TARGET = hello
SRC = $(wildcard *.cpp) 
# wildcard function will search all the matched files and stored their name
OBJ = $(patsubst %.cpp, %.o, $(SRC))
# patsubst will substitute ".cpp" to ".o" for all files in $(SRC)
DEP = $(patsubst %.cpp, %.d, $(SRC))
# patsubst will substitute ".cpp" to ".d" for all files in $(SRC)

$(TARGET): $(OBJ)
	$(CXX) -o $@ $^
# $@ stand for target
# $^ stand for objects

%.o: %.cpp
	$(CXX) -c $(CXXFLAGS) -MMD -MP $< -o $@
# $< stand for the first object
# -MMD: Generate dependency file
# -MP: Create phony targets for each header file

-include $(DEP)
# Include dependency files

.PHONY: clean
clean:
	rm -f *.o *.d $(TARGET)