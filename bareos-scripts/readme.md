# Bareos scripts:

### clean_expired_baros_volumes.sh

This script is usefull if you need to delete a few volumes in the pool choosen by expiration date, pool name and(or) volume status. Basically this script is for autoclean of Bareos storage pool. But you can also gather expiration volumes statistics, running with `--test yes` option.

**Requirments:**

- permissions to run `bconsole` command and acess to **$poolpath** (don't mind if you run this script from bareos Admin Job you're, otherwise you should edit `/etc/sudoers` or run from root).
- git pacakge (`apt` or `yum install git` depending on your linux distro).
- **shflags** library: https://code.google.com/archive/p/shflags/ This script automatically clone this to current directory.

**Usage:**

- Example: `# ./clean_expired_baros_volumes.sh --name Full- --action delete --expire 10 --filter Pruned` will delete 'Pruned' volumes selected by name mask 'Full-' ("Full" storage pool) after 10 days.
- Use `--test yes` key for test mode.
- Or run: `# ./clean_expired_baros_volumes.sh --help` for the help.

On large installations it takes a long time to purge or shift data. May be you also want to force delete some volumes. So you can use Admin Job with this script.

**Bareos Admin Job Example:**

```
Job {
    Name = "Autoclean Pool"
    JobDefs = "DefaultJob"
    Schedule = "Daily"
    Type = "Admin"
    Priority = 1
    # 1 means to run immediately before othe backup jobs

    RunScript {
        Runs When = Before
        Runs On Client = no
        # We don't need to run on the client
        # because this script is for bareos servers and storage deamons
        Fail Job On Error = yes
        Command = "/etc/bareos/bareos-dir.d/clean_expired_baros_volumes.sh --action delete --expire 60 --name Full-"
        # Place this script in bareos director congigs repository and chmod +x
    }
}
```
# Other bareos troubleshooting examples:

Examples how to troubleshoot some things in bareos. All parametrs inside of the scripts.

### clean_missing_volumes.sh
This script physically delete non-existent volumes from Pool in the bareos database. Just set up your `$poolpath` inside the script and run.

### purge_all_volumes_from_pool.sh
Set all voluems in defined pool to "purged" state. Set your `$poolname` inside the script and run.

### remove_purged_volumes.sh
Removes all voluems physically from the disk which are in 'purged' state.
