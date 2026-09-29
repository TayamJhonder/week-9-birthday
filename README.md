# week-9-birthday
this is the code
html

<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Birthdays</title>
        <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-sRIl4kxILFvY47J16cr9ZwB07vP4J8+LH7qKQnuqkuIAvNWLzeN8tE5YBujZqJLB" crossorigin="anonymous">
        <link href="/static/styles.css" rel="stylesheet">
    </head>
    <body>
        <div class="container mt-5">
            <h1 class="mb-4">Birthdays</h1>

            <table class="table table-striped">
                <thead>
                    <tr>
                        <th>Name</th>
                        <th>Birthday</th>
                    </tr>
                </thead>
                <tbody>
                    {% for birthday in birthdays %}
                        <tr>
                            <td>{{ birthday.name }}</td>
                            <td>{{ birthday.month }}/{{ birthday.day }}</td>
                        </tr>
                    {% endfor %}
                </tbody>
            </table>

            <h3 class="mt-5">Add a Birthday</h3>
            <form action="/" method="post">
                <div class="mb-3">
                    <input name="name" placeholder="Name" type="text" class="form-control" required>
                </div>
                <div class="mb-3">
                    <input name="month" placeholder="Month" type="number" min="1" max="12" class="form-control" required>
                </div>
                <div class="mb-3">
                    <input name="day" placeholder="Day" type="number" min="1" max="31" class="form-control" required>
                </div>
                <button type="submit" class="btn btn-primary">Add Birthday</button>
            </form>
        </div>
    </body>
</html>



this is the code of css




body {
    background-color: #f8f9fa;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

h1 {
    color: #0d6efd;
}

table {
    background-color: #ffffff;
    border-radius: 8px;
    overflow: hidden;
}

form {
    background-color: #ffffff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}



this is the code in app.py


import os
from cs50 import SQL
from flask import Flask, flash, redirect, render_template, request, session
from flask_session import Session

app = Flask(__name__)
app.config["SESSION_PERMANENT"] = False
app.config["SESSION_TYPE"] = "filesystem"
Session(app)

db = SQL("sqlite:///birthdays.db")


@app.after_request
def after_request(response):
    response.headers["Cache-Control"] = "no-cache, no-store, must-revalidate"
    response.headers["Expires"] = 0
    response.headers["Pragma"] = "no-cache"
    return response


@app.route("/", methods=["GET", "POST"])
def index():
    if request.method == "POST":
        name = request.form.get("name")
        if not name:
            return redirect("/")

        month = request.form.get("month")
        if not month:
            return redirect("/")
        try:
            month = int(month)
        except ValueError:
            return redirect("/")
        if month < 1 or month > 12:
            return redirect("/")

        day = request.form.get("day")
        if not day:
            return redirect("/")
        try:
            day = int(day)
        except ValueError:
            return redirect("/")
        if day < 1 or day > 31:
            return redirect("/")

        db.execute("INSERT INTO birthdays (name, month, day) VALUES(?, ?, ?)", name, month, day)

        return redirect("/")

    else:
        birthdays = db.execute("SELECT * FROM birthdays")
        return render_template("index.html", birthdays=birthdays)




