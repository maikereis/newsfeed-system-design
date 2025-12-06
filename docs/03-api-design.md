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

### Gerenciamento de Post

```yaml
openapi: 3.0.0
info:
  title: Post Management API
  version: 0.0.0

paths:
  /posts:
    post:
      summary: Create a post
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          multipart/form-data:
            schema:
              type: object
              required:
                - title
                - text
              properties:
                title:
                  type: string
                  maxLength: 100
                text:
                  type: string
                  maxLength: 2200
                media:
                  type: array
                  maxItems: 20
                  items:
                    type: string
                    format: binary
              description: >
                Allowed media: images up to 10MB each, videos up to 60s and total videos size up to 4GB
      responses:
        "201":
          description: Created
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Post"
        "400":
          description: Bad Request
        "401":
          description: Unauthorized

  /posts/{postId}:
    patch:
      summary: Update a post
      description: Allows users to edit their own posts.
      security:
        - bearerAuth: []
      parameters:
        - name: postId
          in: path
          required: true
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/UpdatePostRequest"
      responses:
        "200":
          description: Updated
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Post"
        "400":
          description: Bad Request
        "401":
          description: Unauthorized
        "403":
          description: Forbidden - user cannot edit this post

    delete:
      summary: Delete a post
      description: Allows users to delete their own posts.
      security:
        - bearerAuth: []
      parameters:
        - name: postId
          in: path
          required: true
          schema:
            type: string
      responses:
        "204":
          description: Deleted
        "401":
          description: Unauthorized
        "403":
          description: Forbidden - user cannot delete this post

  /posts/{postId}/comments:
    post:
      summary: Add a comment to a post
      security:
        - bearerAuth: []
      parameters:
        - name: postId
          in: path
          required: true
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/CreateCommentRequest"
      responses:
        "201":
          description: Comment created
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Comment"
        "400":
          description: Bad Request
        "401":
          description: Unauthorized

  /posts/{postId}/likes:
    post:
      summary: Like a post
      security:
        - bearerAuth: []
      parameters:
        - name: postId
          in: path
          required: true
          schema:
            type: string
      responses:
        "204":
          description: Liked
        "401":
          description: Unauthorized

    delete:
      summary: Unlike a post
      security:
        - bearerAuth: []
      parameters:
        - name: postId
          in: path
          required: true
          schema:
            type: string
      responses:
        "204":
          description: Unliked
        "401":
          description: Unauthorized

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:
    UpdatePostRequest:
      type: object
      properties:
        title:
          type: string
          maxLength: 100
        text:
          type: string
          maxLength: 2200
      description: At least one field must be provided

    CreateCommentRequest:
      type: object
      required:
        - text
      properties:
        text:
          type: string
          maxLength: 1000

    Comment:
      type: object
      properties:
        id:
          type: string
        post_id:
          type: string
        user_id:
          type: string
        text:
          type: string
        created_at:
          type: string
          format: date-time

    Post:
      type: object
      properties:
        id:
          type: string
        title:
          type: string
        content:
          type: string
        media_urls:
          type: array
          items:
            type: string
            format: uri
        created_at:
          type: string
          format: date-time
```