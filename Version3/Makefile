CXX = g++
TARGET = hello
OBJ = main.o printhello.o factorial.OBJ

CXXFLAGS = -c -Wall
# -Wall: to display all warnings

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
