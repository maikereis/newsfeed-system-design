# Design da API

Este documento define os contratos de API do sistema de NewsFeed.

---

### Gerenciamento de Usuário

```yaml
openapi: 3.0.0
info:
  title: Account Management API
  version: 0.0.1

paths:

  /v1/login:
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

  /v1/me:
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

  /v1/accounts/{accountId}:
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

  /v1/accounts:
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

  /v1/accounts/{accountId}/follow:
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
  version: 0.0.1

paths:
  /v1/posts:
    post:
      summary: Create a post
      security:
        - bearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
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
                media_urls:
                  type: array
                  maxItems: 20
                  items:
                    type: string
                    format: uri
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

  /v1/posts/{postId}:
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

  /v1/posts/{postId}/comments:
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

  /v1/posts/{postId}/likes:
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

### Gerenciamento de Feed

```yaml
openapi: 3.0.0
info:
  title: Feed Management API
  version: 0.0.1

paths:

  /v1/feed:
    get:
      summary: Get the authenticated user's feed
      description: >
        Returns posts from the authenticated user and accounts they follow,
        ordered by reverse chronological order (newest first).  
        Supports incremental loading via cursor-based pagination.
      security:
        - bearerAuth: []
      parameters:
        - name: limit
          in: query
          schema:
            type: integer
            minimum: 1
            maximum: 50
          description: Number of posts to return (default 20)
        - name: cursor
          in: query
          schema:
            type: string
          description: >
            Pagination cursor for incremental loading.  
            When omitted, returns the first page.
      responses:
        "200":
          description: Feed page
          content:
            application/json:
              schema:
                type: object
                properties:
                  items:
                    type: array
                    items:
                      $ref: "#/components/schemas/FeedPost"
                  nextCursor:
                    type: string
                    nullable: true
                    description: Cursor for the next page or null if no more posts  
        "401":
          description: Unauthorized

  /v1/feed/updates:
    get:
      summary: Check for new posts in the feed
      description: >
        Returns posts more recent than the latest post currently loaded by the client.  
        Useful for refreshing the feed without reloading all items.
      security:
        - bearerAuth: []
      parameters:
        - name: since
          in: query
          required: false
          schema:
            type: string
            format: date-time
          description: >
            Timestamp of the newest post the client has.  
            When omitted, returns the most recent posts.
            When provided, returns posts created after this timestamp.
      responses:
        "200":
          description: New posts available since the given timestamp
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: "#/components/schemas/FeedPost"
        "401":
          description: Unauthorized

components:

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:

    FeedPost:
      type: object
      properties:
        id:
          type: string
        author:
          $ref: "#/components/schemas/Author"
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
        likes_count:
          type: integer
        comments_count:
          type: integer
        is_liked:
          type: boolean
          description: Whether the authenticated user has liked this post

    Author:
      type: object
      properties:
        id:
          type: string
        name:
          type: string
        avatar_url:
          type: string
          format: uri     
```

### Gerenciamento de Interações em Posts

```yaml
openapi: 3.0.0
info:
  title: Post Interactions API
  version: 0.0.1

paths:

  /v1/posts/{postId}:
    get:
      summary: Get a post by id (including interaction counts)
      security:
        - bearerAuth: []
      parameters:
        - name: postId
          in: path
          required: true
          schema:
            type: string
      responses:
        "200":
          description: Post retrieved
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/Post"
        "401":
          description: Unauthorized
        "404":
          description: Post not found

  /v1/posts/{postId}/likes:
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

  /v1/posts/{postId}/comments:
    get:
      summary: List comments for a post
      security:
        - bearerAuth: []
      parameters:
        - name: postId
          in: path
          required: true
          schema:
            type: string
        - name: limit
          in: query
          schema:
            type: integer
            minimum: 1
            maximum: 50
          description: >
            Number of comments to return (default: 20)
        - name: cursor
          in: query
          schema:
            type: string
          description: Pagination cursor for incremental loading
      responses:
        "200":
          description: Comments list
          content:
            application/json:
              schema:
                type: object
                properties:
                  items:
                    type: array
                    items:
                      $ref: "#/components/schemas/Comment"
                  nextCursor:
                    type: string
                    nullable: true
        "401":
          description: Unauthorized

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

  /v1/posts/{postId}/share:
    post:
      summary: Share a post to the user's own feed
      description: >
        Creates a new post that references the original one.
        The shared post may include optional user text.
      security:
        - bearerAuth: []
      parameters:
        - name: postId
          in: path
          required: true
          schema:
            type: string
      requestBody:
        required: false
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/SharePostRequest"
      responses:
        "201":
          description: Post shared
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/SharedPost"
        "401":
          description: Unauthorized
        "404":
          description: Post not found

components:

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:

    InteractionCounts:
      type: object
      properties:
        likes:
          type: integer
          example: 123
        comments:
          type: integer
          example: 4
        shares:
          type: integer
          example: 7

    Post:
      type: object
      properties:
        id:
          type: string
        author_id:
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
        interaction_counts:
          $ref: "#/components/schemas/InteractionCounts"

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

    SharePostRequest:
      type: object
      properties:
        text:
          type: string
          maxLength: 2200
      description: Optional user text when sharing a post

    SharedPost:
      type: object
      properties:
        id:
          type: string
        original_post_id:
          type: string
        user_id:
          type: string
        text:
          type: string
        created_at:
          type: string
          format: date-time
        interaction_counts:
          $ref: "#/components/schemas/InteractionCounts"
```

### Gerenciamento de Notificações

```yaml
openapi: 3.0.0
info:
  title: Notifications API
  version: 0.0.1

paths:
  /v1/notifications:
    get:
      summary: List user notifications
      security:
        - bearerAuth: []
      parameters:
        - name: limit
          in: query
          schema:
            type: integer
            minimum: 1
            maximum: 50
          description: Number of notifications to return (default 5)
        - name: cursor
          in: query
          schema:
            type: string
          description: Pagination cursor
        - name: onlyUnread
          in: query
          schema:
            type: boolean
          description: Return only unread notifications
      responses:
        "200":
          description: Notifications list
          content:
            application/json:
              schema:
                type: object
                properties:
                  items:
                    type: array
                    items:
                      $ref: "#/components/schemas/Notification"
                  nextCursor:
                    type: string
                    nullable: true
        "401":
          description: Unauthorized

  /v1/notifications/{notificationId}/read:
    post:
      summary: Mark a notification as read
      security:
        - bearerAuth: []
      parameters:
        - name: notificationId
          in: path
          required: true
          schema:
            type: string
      responses:
        "204":
          description: Marked as read
        "401":
          description: Unauthorized
        "404":
          description: Notification not found

  /v1/notifications/read-all:
    post:
      summary: Mark all notifications as read
      security:
        - bearerAuth: []
      responses:
        "204":
          description: All marked as read
        "401":
          description: Unauthorized

  /ws/v1/notifications:
    get:
      summary: Real-time notifications via WebSocket
      description: >
        Opens a WebSocket connection that streams notification events 
        in real time.

        **Message Format:**
        All server-sent messages follow the `RealTimeNotification` schema.
      security:
        - bearerAuth: []
      responses:
        "101":
          description: Switching protocols
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/RealTimeNotification"
              x-streaming: true
        "401":
          description: Unauthorized

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:
    NotificationType:
      type: string
      enum:
        - POST_LIKED
        - POST_COMMENTED
        - NEW_FOLLOWER

    UserSummary:
      type: object
      properties:
        id:
          type: string
        username:
          type: string
        avatar_url:
          type: string
          nullable: true
          format: uri

    Notification:
      type: object
      properties:
        id:
          type: string
        type:
          $ref: "#/components/schemas/NotificationType"
        created_at:
          type: string
          format: date-time
        is_read:
          type: boolean
        actor:
          $ref: "#/components/schemas/UserSummary"
        metadata:
          type: object
          description: Additional info depending on notification type
          properties:
            post_id:
              type: string
              nullable: true
            comment_id:
              type: string
              nullable: true

    RealTimeNotification:
      type: object
      description: Payload sent over WebSocket on notification events
      properties:
        event:
          $ref: "#/components/schemas/NotificationType"
        data:
          $ref: "#/components/schemas/Notification"
```