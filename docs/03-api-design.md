# Design da API

Este documento define os contratos de API do sistema de NewsFeed.

---

### Gerenciamento de Usuário

```yaml
openapi: 3.0.0
info:
  title: Account Management API
  version: 0.0.0

paths:

  /login:
    post:
      summary: Authenticate and receive an access token
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/LoginRequest"
      responses:
        "200":
          description: Authentication successful
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/AuthResponse"
        "400":
          description: Bad Request
        "401":
          description: Invalid credentials

  /me:
    get:
      summary: Get authenticated account profile
      security:
        - bearerAuth: []
      responses:
        "200":
          description: Own profile
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Account"
        "401":
          description: Unauthorized

  /accounts/{accountId}:
    get:
      summary: Get an account profile
      security:
        - bearerAuth: []
      parameters:
        - name: accountId
          in: path
          required: true
          schema:
            type: string
      responses:
        "200":
          description: Account profile
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Account"
        "404":
          description: Account not found

    delete:
      summary: Delete an Account
      security:
        - bearerAuth: []
      parameters:
        - name: accountId
          in: path
          required: true
          schema:
            type: string
      responses:
        "204":
          description: Deleted

  /accounts:
    post:
      summary: Create an Account
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/CreateAccountRequest"
      responses:
        "201":
          description: Account created
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Account"
        "400":
          description: Bad Request

  /accounts/{accountId}/follow:
    post:
      summary: Follow another account
      security:
        - bearerAuth: []
      parameters:
        - name: accountId
          in: path
          required: true
          schema:
            type: string
      responses:
        "204":
          description: Followed
        "404":
          description: Account not found

    delete:
      summary: Unfollow an account
      security:
        - bearerAuth: []
      parameters:
        - name: accountId
          in: path
          required: true
          schema:
            type: string
      responses:
        "204":
          description: Unfollowed
        "404":
          description: Account not found

components:

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:

    LoginRequest:
      type: object
      required: [email, password]
      properties:
        email:
          type: string
          format: email
          maxLength: 100
        password:
          type: string
          maxLength: 256

    AuthResponse:
      type: object
      properties:
        accessToken:
          type: string
        tokenType:
          type: string
          example: Bearer
        expiresIn:
          type: integer
          description: Expiration time in seconds
        refreshToken:
          type: string
          description: Optional refresh token (if you support refresh)

    CreateAccountRequest:
      type: object
      required: [name, email, password, birthDate]
      properties:
        name:
          type: string
          maxLength: 100
        email:
          type: string
          format: email
          maxLength: 100
        password:
          type: string
          maxLength: 256
        birthDate:
          type: string
          format: date

    Account:
      type: object
      properties:
        id:
          type: string
        name:
          type: string
        email:
          type: string
        birthDate:
          type: string
          format: date
        createdAt:
          type: string
          format: date-time
        followersCount:
          type: integer
        followingCount:
          type: integer
        isFollowing:
          type: boolean
          description: Whether the requester follows this account (optional)
```