These folders `master` and `4.6` will eventually become branches.

For now, they're folders because it makes the migration a little easier. After finishing the migration, I'll turn the folders into branches.

During the migration, files that should be identical between `master` and `4.6` will only be kept in `master`. When a file needs to eventually have different content in the `master` and `4.6` branches, then during the migration I'll use two separate files. 
