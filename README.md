# fetchmail-multi
Run multiple copies of fetchmail as the same user

Fetchmail supports IMAP IDLE, which is a really useful feature. However, using
it with more than one server isn't supported by fetchmail.

Attached to this README is a systemd service template which can be used to
run multiple copies of fetchmail in parallel, all feeding into the same
mailbox.

Since fetchmail instances are unaware of each other, the MDA you use with
fetchmail must provide suitable locking facilities, so that only one
fetchmail instance at a time writes to the mailbox.

# Setup
# Main
1) Edit `fetchmail-multi@.service`:-
   - set the service `User` and `Group` to be those of the non-privileged
     user you wish to use to run fetchmail. The rest of this file assumes
     these are both `vmail`.
   - Check the paths in the file are correct for your setup.
2) Copy `fetchmail-multi@.service` to `/lib/systemd/system` or equivalent.
3) `sudo systemctl daemon-reload`
4) Create `/etc/fetchmail.d` owned by root, and readable by only the
   `vmail` group:-

   ```
   $ ls -ld /etc/fetchmail.d/
   drwxr-x--- 2 root vmail 4096 Jul 18 17:06 /etc/fetchmail.d/
   ```
# Per-server
1) Edit `example.conf`, making the necessary changes for your own system.
2) Copy `example.conf` to `/etc/fetchmail.d/<instance>.conf` where `<instance>`
   is a descriptive name for your mailserver. We'll use `mailserver1` as
   the `<instance>` here.
3) Make sure the permissions on the file are 0600, and the owner:group is
   `vmail:vmail`. This is checked by `fetchmail` itself.
4) Enable and start the service with:-
   ```
   sudo systemctl enable --now fetchmail-multi@mailserver1
   ```
5) Check the service status in the usual ways.

# Feedback
If this works for you, or you'd like to make suggestions, please do!
