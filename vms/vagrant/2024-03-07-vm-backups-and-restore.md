<h1>Vagrant: Backups and Restore</h1>
* It is important that if I'm going to be managing vms on a system, that I also familiarize myself on taking backups and restore
* NOTE: Be advised that not all providers support this
  - However in my case, vbox has support for this

<h2>Backups</h2>
* `Vagrant` has a snapshot feature that allowss you to save a current state of a vm
  - `vagrant snapshot save <snapshot>`: takes a snapshot of the current state of the vm
    * May cause some problems to take a snapshot while the vm is running, so run a `vagrant halt` first
  - `vagrant snapshot list`: lists all snapshots you've take
  - `vagrant snapshot delete <snapshot>`: deletes a snapshot

<h2>Restore</h2>
* `Vagrant` has a restore for said snapshots, to restore a vm to an earlier state
  - `vagrant snapshot restore <snapshot>`: will restore a vm back to the state of that snapshot
    * May cause some problems to restore a snapshot while the vm is running, so run a `vagrant halt` first
