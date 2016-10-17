# libgmxfort

This is a Fortran library for reading in an analyzing GROMACS compressed
trajectory files (.xtc) and index files (.ndx). 

## Requirements

xdrfile is required.

## Installation

    $ make
    # make install

## Usage

Compile your program with `-lgmxfort`. You may also need to use `-I` to point to
where the modules files are (by default at `/usr/include`.

To use the library always put `use gmxfort_trajectory` for using the
`Trajectory` class and `use gmxfort_utils` for using any of the other utilities.
There is an example in the `example` folder on how to do this.

Typically you will open a trajectory file (and optionally a corresponding index
file). Then you will read in the entire trajectory file at once, or you can read
it in in chunks. Then you should close the trajectory file when done.

First, always construct a `Trajectory` object:

    type(Trajectory) :: trj

Then open the file:

    trj%open("traj.xtc")

If you have a corresponding index file you would do this instead:

    trj%open("traj.xtc", "index.ndx")

After opening you can now read the xtc file in. Here's how to read it in all at
once:

    trj%read()

Now every atom's coordinates are accessible via `x`. For exmaple, to get the
coordinates of the first atom in the first frame you would do the following. The
frame is the first argument and the atom number is the second argument. 

    real :: myatom(3)
    myatom = trj%x(1, 1)

If you read in an index file, you can get atom coordinates in relationship to
that. The following gets the fifth atom in index group `C` in the `10`th frame:

    myatom = trj%x(10, 5, "C")

If you want to read in the trajectory file in chunks use `read_next`. By default
it reads in one frame:

    trj%read_next()

To read in more than one, specify an argument. The following reads in 10 frames:

    trj%read_next(10)

Note that when you access `x` you will still have to give it the frame number as
the first argument, even if you only read in one frame. `read_next` actually
returns the number of frames read in. This is useful to near the end of the
file. Additionally you can always get the number of frames stored in a
trajectory object with the `nframes` member:

    trj%nframes

You can also get the number of atoms with the `n` method:

    trj%n

If you want to know how many atoms are in an index group include the group name
as an argument. In this example the group name is "C":

    trj%n("C")

To get the box coordinates, use `b`. The following get the box of the `2`nd
frame:

    real :: mybox(3,3)
    mybox = trj%b(2)

## License
