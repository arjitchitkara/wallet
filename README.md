# Wallet

## Getting Started

1. Clone the repository:

    ```bash
    git clone https://github.com/arjitchitkara/wallet.git
    ```

2. Install dependencies:

    ```bash
    npm install
    ```

3. Run PostgreSQL either locally or on the cloud (e.g., neon.tech):

    ```bash
    docker run -e POSTGRES_PASSWORD=mysecretpassword -d -p 5432:5432 postgres
    ```

4. Copy all `.env.example` files to `.env`.

5. Update `.env` files with the correct database URL.

6. Set up the database:

    ```bash
    cd packages/db
    npx prisma migrate dev
    npx prisma db seed
    ```

7. Start the user app:

    ```bash
    cd apps/user-app
    npm run dev
    ```

8. Try logging in using:
    - Phone: `1111111111`
    - Password: `alice`
    
    (Refer to `seed.ts` for more details.)

## Transferring Money

To transfer money from the bank:

1. Enter the amount.
2. Select the bank.
3. Click on "Add Money".

    ![Add Money](packages\PNG\TRANSFER.png)

4. Wait for the website to redirect you to the bank's website.
5. Without doing anything on the bank's website, go back to the transfer page.
6. In another terminal, navigate to `apps/bank-webhook` and run:

    ```bash
    npm run dev
    ```

7. Open another terminal, go to `packages/db`, and run:

    ```bash
    npx prisma studio
    ```

8. Open the OnRampTransaction table in Prisma Studio, and find the recent processing transaction. Copy the `token`, `user_identifier`, and `amount`.

    ![Prisma Studio](packages\PNG\ONRAMP.png)

9. Go to Postman and make a POST request to:

    ```
    http://localhost:3003/hdfcWebhook
    ```

    with the following body:

    ```json
    {
      "token": "458.45458219267266",
      "user_identifier": "1",
      "amount": "20000"
    }
    ```

    (Change the values accordingly.)

10. You should receive a response:

    ```json
    {"message": "Captured"}
    ```

11. Refresh the webpage, and the balance will be updated. Everything else should work perfectly.
