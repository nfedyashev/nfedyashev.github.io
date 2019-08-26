cat some_script.rb | heroku run console --app=my-heroku-app --no-tty

One-Liner
The run console command will read input from stdin and pass it to the remote console session. Knowing this, you can run a simple Rails query:

BASH
echo "puts User.count" | heroku run console --app=my-heroku-app
The result will be displayed in the terminal, but you’ll still be in the Heroku console session. One way to avoid that would be to force the session to end by tacking on an exit to your one-liner.

BASH
echo "puts User.count; exit" | heroku run console --app=my-heroku-app
