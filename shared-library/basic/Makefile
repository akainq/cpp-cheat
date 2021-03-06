CC_DL = gcc -ggdb3 -std=c89 -Wall -Wextra
CC = $(CC_DL) -pedantic-errors

.PHONY: all clean run

all: maina.out mainso.out mainso_fullpath.out dlopen.out

run: all
	./maina.out
	LD_LIBRARY_PATH=. ./mainso.out
	./mainso_fullpath.out
	LD_LIBRARY_PATH=. ./dlopen.out

mainso.out: main.o libcirosantilli_ab.so
	@# Will look for lib with basename *exactly* `libcirosantilli_ab.so`,
	@# `libcirosantilli_ab.so.1` will not do!
	$(CC) -L'.' main.o -o mainso.out -lcirosantilli_ab
	@#
	@# With ':' uses full basename.
	@# Only works for basename, not absolute path.
	@# Application: select an specific version such as `libcirosantilli_ab.so.1`
	@##$(CC) -L"." main.o -o mainso.out -l:libcirosantilli_ab.so
	@#env LIBRARY_PATH=$LIBRARY_PATH:. $(CC) main.c -o mainso.out -lcirosantilli_ab
	@#
	@# TODO how to use rpath?
	@#$(CC) -Wl,-rpath,. main.o -o mainso.out -lcirosantilli_ab

# This is not recommended.
#
# Better use linker path as in mainso.out.
#
# readout -d shows that the ouptut stores the full path.
#
mainso_fullpath.out: main.o libcirosantilli_ab.so
	$(CC) main.o '$(shell printf "`pwd`/libcirosantilli_ab.so")' -o mainso_fullpath.out
	@# Does not work
	@#$(CC) main.o -o mainso_fullpath.out -l"$(shell realpath libcirosantilli_ab.so)"

maina.out: main.o ab.a
	$(CC) main.o ab.a -o maina.out

# If we use -pedantic-errors, build fails:
#
#     ISO C forbids conversion of object pointer to function pointer type
#
# There seems to be no no undefined way of doing it:
# https://stackoverflow.com/questions/14134245/iso-c-void-and-function-pointers
#
# -dl required otherwise: https://stackoverflow.com/questions/956640/linux-c-error-undefined-reference-to-dlopen
dlopen.out: dlopen.c
	$(CC_DL) -o '$@' '$<' -ldl

libcirosantilli_ab.so: a.o b.o
	$(CC) -shared a.o b.o -o libcirosantilli_ab.so
	@# Same?
	@#$(CC) -shared -Wl,-soname,libcirosantilli_ab.so a.o b.o -o libcirosantilli_ab.so

ab.a: a.o b.o
	ar rcs ab.a a.o b.o

%.o: %.c
	$(CC) -fPIC -c '$<' -o '$@'

clean:
	rm -rf *.o *.a *.so *.out
