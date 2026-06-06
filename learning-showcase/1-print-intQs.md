## FastAPI

### 1. Event Loop

* **What:** The main engine inside FastAPI that manages and switches between different tasks.
* **Why:** It allows FastAPI to handle thousands of users at the same time on a single server thread without slowing down.
* **How:** It looks at a list of tasks, pauses tasks that are waiting for data (like a database response), works on other ready tasks, and comes back when the data is ready.
* **Example:** A FastAPI server answering a quick homepage request for one user while another user is waiting for a slow database search.
* **Analogy:** A single fast-food cashier who takes your order, hands the ticket to the kitchen, and immediately helps the next customer instead of standing still waiting for your food to cook.

### 2. Async vs Sync

* **What:** Sync (Synchronous) means tasks run one after another in a strict line. Async (Asynchronous) means tasks can pause and let other work happen while they wait.
* **Why:** Sync code wastes time doing nothing while waiting for databases or websites, while Async keeps the computer busy and fast.
* **How:** Sync blocks the server until a task is completely finished. Async uses `async/await` keywords to temporarily step aside when hitting a waiting bottleneck.
* **Example:** A standard Python endpoint blocks the whole server during a database lookup, but an `async def` endpoint lets other users browse the site during that same lookup.
* **Analogy:** Sync is a chess player who sits and stares at the board until the opponent moves. Async is a master player playing 50 people at once, moving to the next table while someone thinks.

### 3. Celery vs Background Tasks

* **What:** FastAPI `BackgroundTasks` run small extra jobs inside the same app, while `Celery` is a completely separate system built for heavy, time-consuming work.
* **Why:** Running heavy tasks inside FastAPI will freeze the website for your users, so massive workloads must be sent away to separate servers.
* **How:** `BackgroundTasks` triggers a function right after the web response finishes. `Celery` sends the job packet to a manager (like Redis), where independent worker computers pick it up later.
* **Example:** Use `BackgroundTasks` to send a quick order confirmation email. Use `Celery` to resize 10,000 product images.
* **Analogy:** `BackgroundTasks` is your waiter wrapping up your leftovers at the table before helping someone else. `Celery` is sending a pile of dirty dishes to the kitchen staff so the waiter never stops serving.

### 4. Scaling FastAPI

* **What:** The strategy of adding more server power or copying your app across multiple computers to handle heavy user traffic.
* **Why:** A single Python process can only use one CPU core at a time, which limits how many users it can support before crashing.
* **How:** You use software like Uvicorn or Gunicorn to run multiple copies of your app on one machine, put them inside Docker containers, and use a load balancer to split traffic among them.
* **Example:** Running four Uvicorn workers inside an automated cloud cluster that launches new server copies whenever traffic spikes.
* **Analogy:** Opening more checkout lanes at a busy grocery store and putting a manager at the front door to guide shoppers to the shortest line.

### 5. API Performance Tuning

* **What:** The practice of finding and fixing slow parts of your code to make the website load faster.
* **Why:** Slow websites make users leave and lose businesses money, while wasting expensive cloud server resources.
* **How:** You limit data size using Pydantic models, turn on data compression (like Gzip), reuse database connections, and fix queries that accidentally pull too much data.
* **Example:** Changing an endpoint so it only sends back a product's name and price instead of sending the entire database row.
* **Analogy:** Tuning up a racing car by removing heavy parts you don't need, smoothing out the shape, and cleaning the fuel lines.

---

## JWT (JSON Web Tokens)

### 6. Access Token vs Refresh Token

* **What:** An access token proves who you are for a short time. A refresh token is a secure key used only to get a new access token when the old one dies.
* **Why:** If someone steals your short-lived access token, they can only use it for a few minutes. The long-lived refresh token is kept safely hidden away.
* **How:** The browser sends the access token with every single web request. It only sends the refresh token to a special `/refresh` link once in a while.
* **Example:** An access token lasts for 15 minutes to keep your session valid, while a refresh token lasts for 7 days so you don't have to type your password every day.
* **Analogy:** An access token is a plastic wristband you get at a theme park for the day. A refresh token is your paper receipt you keep safe in your pocket to get a new wristband tomorrow morning.

### 7. JWT Revocation

* **What:** A system used to cancel or destroy a token before its official expiration time runs out.
* **Why:** If a user logs out or changes their password, you must stop their old tokens from accessing the site immediately.
* **How:** You save the stolen or logged-out token ID inside a fast Redis storage memory with a self-destruct timer, and check that list on every request.
* **Example:** A user clicks 'Log Out'. Your FastAPI app saves that token's ID into Redis. If someone tries to use that token a second later, the app blocks them.
* **Analogy:** A security guard checking a list of canceled membership cards at the door to stop banned people from entering, even if their card looks real.

### 8. Secure Storage

* **What:** The safest way to save user login tokens inside a web browser.
* **Why:** Saving tokens in regular browser storage (`localStorage`) makes them easy targets for hacker scripts (XSS attacks) to steal.
* **How:** You store tokens inside specialized cookies marked as `HttpOnly` and `Secure`, which blocks browser scripts from ever touching them.
* **Example:** Setting a token cookie from your FastAPI backend with the `httponly=True` setting so JavaScript cannot read it.
* **Analogy:** Putting your expensive jewelry inside a bank vault instead of leaving it in a clear plastic box on your front porch table.

### 9. Token Rotation

* **What:** A safety rule where every time you use a refresh token, the server takes it away and gives you a brand new pair of tokens.
* **Why:** It stops hackers from reusing stolen tokens. If a token is used twice, the server knows something is wrong and locks the account down.
* **How:** The database tracks token versions. If an old version shows up, the server cancels the entire family of tokens and forces a new login.
* **Example:** A user uses `Refresh Token 1` and gets `Refresh Token 2`. If a hacker tries to use `Refresh Token 1` again, the server blocks both of them.
* **Analogy:** A secret spy meeting where the password changes every single time you use it. If anyone says an old password, the alarms go off.

### 10. Key Rotation

* **What:** Regularly changing the secret master passwords that your server uses to create and verify login tokens.
* **Why:** It ensures that even if a secret key is leaked or guessed over a long period, old keys become useless quickly.
* **How:** The server keeps a list of valid keys. It uses a new key to create tokens but keeps an old key active for a few days to check older valid tokens.
* **Example:** Switching your server from using `SecretKey_A` to `SecretKey_B` at the start of the month without forcing active users to log back in.
* **Analogy:** Changing the physical master locks on apartment complex gates every year, giving out new keys ahead of time so no one gets locked out.

---

## PostgreSQL

### 11. Explain Analyze

* **What:** A database troubleshooting command that runs a search query and shows you a step-by-step report of how long it took.
* **Why:** Without it, finding out why a database search is slow is just guesswork. It reveals if the database is using shortcuts or scanning everything.
* **How:** You type `EXPLAIN ANALYZE` right before your SQL query in your database tool and read the text report that comes out.
* **Example:** Running `EXPLAIN ANALYZE SELECT * FROM products WHERE price < 10;` to see if the database is using your price index.
* **Analogy:** A factory inspector who stands by a worker with a stopwatch to write down exactly how many seconds every single step of the job takes.

### 12. Composite Indexes

* **What:** A single database index helper built using two or more columns at the same time in a specific order.
* **Why:** It speeds up complex searches that filter or sort data by multiple details at once, like searching by category *and* price together.
* **How:** You run a database command to create the index, placing columns from the most specific filter to the most general filter.
* **Example:** Creating a combined index on `(category_id, price)` to quickly show a list of shoes sorted from cheapest to most expensive.
* **Analogy:** A physical office filing cabinet that is organized first by Department Name, and then internally sorted by Employee ID.

### 13. Partial Indexes

* **What:** An index that only maps out a small section of a table based on a specific rule.
* **Why:** It saves server hard drive space and speeds up searches by completely ignoring rows that you rarely look for.
* **How:** You add a `WHERE` filter to your index creation command so it only tracks rows that match your rule.
* **Example:** Creating an index on an orders table `WHERE status = 'processing'`, ignoring the millions of old orders that are already delivered.
* **Analogy:** A small VIP guest notebook that only lists active celebrities currently in the building, rather than a giant phone book of every citizen in the city.

### 14. Partitioning

* **What:** Splitting up one giant database table into several smaller physical tables behind the scenes.
* **Why:** When tables get too big, searches become slow. Splitting them up keeps data sizes manageable and fast.
* **How:** You tell the database to automatically break a table apart using a key, like separating data by order dates.
* **Example:** Splitting a sales table so that data from 2024, 2025, and 2026 are kept in separate hidden sub-tables.
* **Analogy:** Splitting a massive encyclopedia set into 26 different letter books instead of binding all the millions of pages into one single heavy book.

### 15. Read Replicas

* **What:** Exact copy databases that follow a main database and are used only to answer view-only data searches.
* **Why:** It stops the main database from getting overwhelmed by users who are just browsing, leaving it free to handle payments and updates safely.
* **How:** The main database streams updates to copy databases. Your code sends search requests to the copies and save requests to the main server.
* **Example:** Thousands of shoppers looking at product pictures hit three copy databases, while the final purchase button talks to the main database.
* **Analogy:** An author who writes a book draft alone in their room (Main) while a printing press copies the book for thousands of readers to look at (Replicas).

---

## Redis

### 16. Cache Aside

* **What:** A code pattern where your app talks to a fast temporary memory store (Redis) and a permanent database separately.
* **Why:** It ensures you only save data in your fast memory when a user actually asks to see it, which saves valuable memory space.
* **How:** The app looks in Redis first. If the data is there, it uses it. If it is missing, it reads from the database, saves it to Redis for next time, and returns it.
* **Example:** Checking Redis for `product_4`. If it's a miss, fetch it from PostgreSQL, run `redis.set()`, and show the user.
* **Analogy:** A student looking for a word definition in their personal notebook first. If it is blank, they walk to the library shelf, read it, and write it in their notebook for later.

### 17. Cache Stampede

* **What:** A system slowdown that happens when a popular piece of cached data expires, causing thousands of users to hit the database at the same moment.
* **Why:** A sudden flood of identical searches can crash your primary database or freeze your website.
* **How:** You use temporary locks so only the first user updates the data while others wait, or you update the data in the background before it expires.
* **Example:** A popular sale page cache dies at midnight. The first user request locks the system to rebuild the cache, keeping other users from overloading PostgreSQL.
* **Analogy:** A popular celebrity at a booth who steps out for a quick break, causing the crowd to rush the backroom door all at once instead of waiting for a representative.

### 18. Pub/Sub (Publish/Subscribe)

* **What:** A live messaging system where apps broadcast news to a channel without needing to know who is listening.
* **Why:** It sends information instantly across separate services without wasting time constantly asking a database for updates.
* **How:** One service sends a message to a channel name, and any other services listening to that channel name receive it instantly.
* **Example:** Sending a real-time price drop alert to thousands of active shoppers browsing your web store simultaneously.
* **Analogy:** A local radio station playing music over a specific frequency. They don't know who is listening, but anyone tuned to that station hears the song instantly.

### 19. Distributed Locks

* **What:** A digital key used to make sure only one server instance can work on a specific piece of data at a time.
* **Why:** It prevents errors and double-processing when two separate servers try to change the exact same item at the same millisecond.
* **How:** A server creates a unique key in Redis with a strict time limit. Other servers see this key and wait until it is deleted before trying to do that work.
* **Example:** Preventing an item with only 1 left in stock from being accidentally sold to two different people buying it at the exact same moment.
* **Analogy:** A single physical key to a shared office bathroom. No matter how many people need to use it, only the person holding the key can unlock the door and walk in.

### 20. Redis Cluster

* **What:** A network of several Redis servers connected together to share data storage and keep running if one machine breaks.
* **Why:** A single Redis server can run out of memory space and can crash your entire site if it goes offline.
* **How:** Your data keys are automatically split up and shared across multiple master and copy servers.
* **Example:** Splitting millions of user login sessions across a 6-server Redis network so no single machine gets overloaded.
* **Analogy:** A large library system split into multiple neighborhood branches. If one branch closes, the other backup branches keep serving readers.

---

## Docker

### 21. Multi-Stage Build

* **What:** A method of creating clean Docker containers by using temporary build spaces that discard heavy tools before finishing.
* **Why:** It keeps production container sizes very small and secure by leaving out code compilers and files that are only needed during development.
* **How:** You use one heavy Docker image to compile your files, then copy just the finished, clean outputs into a tiny, bare-bones production image.
* **Example:** Using a 1GB image to build python files, then moving just the finished code packages into a slim 100MB runtime container.
* **Analogy:** A chef using a messy, tool-filled prep kitchen to bake a cake, then moving just the clean, finished cake onto a serving plate to take to the guests.

### 22. Health Checks

* **What:** A setting that tells Docker how to check if the application inside a container is truly working or frozen.
* **Why:** A container process can look like it is running fine from the outside while being internally frozen or broken on the inside.
* **How:** You write a test command inside the configuration file that pings an internal web link like `/health` every few seconds.
* **Example:** Setting Docker to check your FastAPI `/health` page. If the database connection drops for three checks in a row, Docker restarts the container automatically.
* **Analogy:** A supervisor who pokes a worker on the shoulder every hour to make sure they are awake and alert, rather than assuming they are fine just because they are sitting in their chair.

### 23. Container Security

* **What:** The process of locking down your Docker containers to protect them from hackers and safety issues.
* **Why:** Default containers often run as the system 'root' user, meaning a bug in your code could give a hacker full control over your entire computer infrastructure.
* **How:** You change the container user settings to a limited account, block file writing permissions, and run safety scan tools on your setup files.
* **Example:** Adding a non-root user setting inside your deployment script so your web app can never change system-level operating files.
* **Analogy:** Renting a room to a tenant but giving them an isolated key that only opens their specific bedroom door, instead of giving them the master key to the whole building.

### 24. Secrets

* **What:** Highly sensitive configuration data like database passwords, API keys, and master login details.
* **Why:** Putting passwords directly into your source code or image files allows anyone who sees the code to steal them.
* **How:** You pass secrets into the container at runtime using external environment variables or temporary encrypted storage files.
* **Example:** Reading your core database link using Python's `os.getenv("DATABASE_URL")` instead of writing the actual password text into the file.
* **Analogy:** A wall safe where you type in a fresh code to open the office door every morning, rather than permanently carving the combination lock into the wooden door itself.

### 25. Production Deployment

* **What:** The automated process of building, testing, and shipping your Docker containers safely to live internet servers.
* **Why:** Moving files onto servers by hand leads to mistakes, configuration differences, and website downtime for your users.
* **How:** You use automated deployment pipelines (like GitHub Actions) to run tests, build clean images, and update your servers without stopping the website.
* **Example:** Pushing new code changes to GitHub, which automatically checks for bugs, updates the container, and swaps it onto your web server seamlessly.
* **Analogy:** An automated train swap system that changes old train cars for new ones at top speed without stopping the train or bothering the passengers.

---

## Architecture

### 26. Modular Monolith vs. Microservices

* **What:** A Modular Monolith keeps your entire app code together but separates features into clean internal folders. Microservices break features out into completely separate mini-apps that talk over the network.
* **Why:** Microservices add complex network setup, slow down communications, and increase server bills, while a Modular Monolith is easier to build, test, and ship for most teams.
* **How:** You structure your folders cleanly (like separating `/orders` and `/catalog`) but let them talk directly via fast Python imports rather than slow web links.
* **Example:** Keeping your Cart and Product lists inside one single codebase, but blocking the Product code from directly editing the Cart's database tables.
* **Analogy:** A Modular Monolith is a department store divided into clean sections (clothing, electronics) under one roof. Microservices are an open market where every stall is a separate building down the street.

### 27. Scalability Strategy

* **What:** Your plan for handling growth by making sure you can add more servers easily when more users join your site.
* **Why:** Apps break during busy sales if they aren't designed to share work across multiple computers smoothly.
* **How:** You keep your web nodes completely stateless (no local user sessions saved on the server), offload heavy database tasks to copies, and use message queues for slow background work.
* **Example:** Keeping user logins inside a central Redis cache, allowing you to add 10 new web servers during a big sale without disconnecting active users.
* **Analogy:** A shipping company buying more delivery vans (Horizontal) and upgrading to larger trucks (Vertical) to move a massive pile of holiday packages smoothly.

### 28. High Availability (HA)

* **What:** Designing your system layout so it stays online and works properly even if parts of your hardware break down completely.
* **Why:** Hardware fails and data centers lose power. Without redundant backups, a single server crash takes your entire business offline.
* **How:** You run copies of your app in different geographic regions, use smart load balancers to skip broken servers, and set up automatic database failover switches.
* **Example:** Running your FastAPI app in two different cloud data centers at once; if one data center completely loses power, your traffic switches to the other copy instantly.
* **Analogy:** A commercial airplane built with two separate engines and duplicate backup computers; if one fails mid-flight, the secondary systems keep the plane flying safely.

### 29. AI Integration

* **What:** Adding machine learning features (like product recommendations) into your core application logic safely.
* **Why:** Running live AI calculations on your web server is slow and expensive, which can ruin the browsing speed for your regular shoppers.
* **How:** You compute AI results in the background using worker queues, save them into vector database extensions (like `pgvector`), and pull them instantly using fast queries.
* **Example:** Generating product recommendations once a day using a background job, saving them to PostgreSQL, and displaying them instantly during checkout.
* **Analogy:** A restaurant offering meal tips by printing personalized profile cards ahead of time, instead of making the chef interview every diner at the table while their food gets cold.

### 30. Technical Debt Management

* **What:** The balance between writing messy code quickly to meet a deadline and taking time to clean it up so it doesn't slow down future work.
* **Why:** Ignoring messy code (technical debt) makes your app fragile, full of bugs, and incredibly difficult to update later on.
* **How:** You log shortcuts in your task tracker, dedicate a fixed part of every work cycle to cleanup, and run automated testing checks on every code change.
* **Example:** Rushing an application launch with a manually updated database to hit an event deadline, but scheduling the next full week to build automated migration tracking files.
* **Analogy:** Taking out a cash loan to open your store quickly; you launch on time, but you must pay back the money regularly to prevent debt from taking over your business.
