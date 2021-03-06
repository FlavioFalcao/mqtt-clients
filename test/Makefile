TESTS=$(shell ls test_*.c | cut -d'.' -f1 | sed 's/^test_//')
# Specify your preferred editor which should open a new test file/group.
# Set to `true` to do nothing.
EDITOR ?= true

TEST_GROUP_TEMPLATE=bin/group.c.template
CFLAGS += -Wall -Wp -w -g -I./unity -I../src -DTEST
UNITY_SRC=https://codeload.github.com/ThrowTheSwitch/Unity/zip/master
TEST_RUNNER=.run_tests

UNAME_S := $(shell uname -s)
ifeq ($(UNAME_S),Darwin)
	CFLAGS += -gdwarf-2 -g -O0
endif

# Execute test runner after compilation
default: compile
	./$(TEST_RUNNER)

clean:
	rm -f $(TEST_RUNNER) $(TEST_RUNNER).c

# Downloads and extracts unity sources into ./unity
bootstrap:
	curl $(UNITY_SRC) > .unity.zip && \
	unzip .unity.zip Unity-master/src/* Unity-master/extras/fixture/src/* -d unity &&\
	mv unity/Unity-master/src/* unity/Unity-master/extras/fixture/src/* unity && \
	rm -rf unity/Unity-master .unity.zip

# Creates the test runner source file which calls
# all TEST_GROUPs defined by $(TEST)
assemble:
	./bin/assemble_groups.sh $(TESTS) > $(TEST_RUNNER).c

# Compiles the test runner created by `assemble`
compile: assemble
	gcc $(CFLAGS) \
	unity/unity.c unity/unity_fixture.c $(SRC) -o $(TEST_RUNNER) \
	$(foreach var,$(TESTS), test_$(var).c) $(TEST_RUNNER).c

# Creates a new TEST_GROUP `test_<name>.c` from $(TEST_GROUP_TEMPLATE)
# @param[name] : the TEST_GROUP name
new:
	test -n "$(name)" && ! test -f "test_$(name).c" && \
	cat $(TEST_GROUP_TEMPLATE) | sed "s/\<GROUP\>/$(name)/g" > test_$(name).c && \
	$(EDITOR) test_$(name).c \
