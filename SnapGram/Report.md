# Report for SnapGramApp:
### Title: #### Broken Access Control ####
### Description: ####
[SnapGram](https://snapgramapp.vercel.app/) is a web application where users can post pictures, like, comment, and follow each other. During the web assessment I noticed "user profiles and posts" were being referenced by a randomly generated ID which made the website vulnerable to Broken Acess Control allowing an attacker create new posts or edit existing posts for victim users.
- Profile ID:

![ProfileID](https://github.com/bugkay101/Web-Assessments/assets/149082141/dbe9c56f-5dc9-4d25-9e10-d364ddbc017b)

Here we can see the profile ID has a value of ```663d55ebc74486976e37449c```

- Post ID:

![PostID2](https://github.com/bugkay101/Web-Assessments/assets/149082141/2c788ff6-322d-48ea-bc88-207c6f8a1f59)

Here we can see the post ID has a value of ```663f3880560bb5684656a2b4```

## Proof Of Concept:
I created two user accounts to abuse the Create Post (used to create a new post) and Update Post (used to edit an existing post) functionality
### Abusing the Create Post functionality:
#### Step 1: Creating Users
To achieve this I created two accounts (an attacker and a victim account):
- Attacker User (bugkay231):

![Attacker](https://github.com/bugkay101/Web-Assessments/assets/149082141/c34e2cb4-9041-4d7a-8e89-8df729feafa6)

- Victim User (coolguy):

![Victim](https://github.com/bugkay101/Web-Assessments/assets/149082141/d1f9235e-8378-438a-bfcc-7df5d9624474)

Notice the coolguy's profile ID ```663d5a1cc74486976e3744a3``` is highlighted, we'll be using this parameter to perform our attack

#### Step 2: Creating Posts:
On the attacker's account, we'll attempt to create a post and find an entry point where we can inject the victim's (coolguy) profile ID which will make the post appear on the Victim's profile. 

![Create](https://github.com/bugkay101/Web-Assessments/assets/149082141/1ffa6847-d3e9-41fa-be28-84f8a647dd8b)

![Create2](https://github.com/bugkay101/Web-Assessments/assets/149082141/62664f5c-8a08-493d-b787-057d075fc18b)

Now we'll be intercepting HTTP traffic with ```burpsuite``` so we can tamper with the HTTP requests being made to create a post

![Burp1](https://github.com/bugkay101/Web-Assessments/assets/149082141/c96645c6-ec46-4da2-830f-076f73268e1b)

Keep forwarding until we find the entry point

We end up finding out that the website makes a ```POST``` request to the server and submits Certain parameters on line 16

![Burp2](https://github.com/bugkay101/Web-Assessments/assets/149082141/952b591d-1ca5-48aa-9591-22c95f097c81)

Notice the ```Creator``` parameter has a value of ```663d55ebc74486976e37449c``` which is bugkay231's (the attacker) profile ID.

We'll change it to coolguy's profile ID and forward the subsequent requests, this will make the created post appear on coolguy's profile making it seem like coolguy made the post.

![Victim2](https://github.com/bugkay101/Web-Assessments/assets/149082141/e4e06161-94ef-42c1-a84a-733b0bde72ce)

### Abusing the Update Post functionality:
To achieve this we'll be using the two user accounts from the previous attack.
#### Step 1 - Create a Post with the attacker's account:
We'll create a post with bugkay231's account:

![Update](https://github.com/bugkay101/Web-Assessments/assets/149082141/f3f7dd50-1869-41fb-8043-a3a91b62a2cd)

#### Step 2 - Edit the post on the attacker's account:
Well attempt editing bugkay231's post but this is not our real intentions. What we really want is to edit the victim's (coolguy) post.

![Update2](https://github.com/bugkay101/Web-Assessments/assets/149082141/59e5b277-a13f-455c-b841-a305e256fd0c)

If we look into the URL bar in the screenshot above we can see the attacker's Post ID is ```663fa65472bd46d3cffb03df```
#### Step 3 - Changing the post ID:
Here's a post fom the victim profile, take note of the picture, the caption, and the post ID in the URL bar

![Update3](https://github.com/bugkay101/Web-Assessments/assets/149082141/86e3d7ce-122c-4206-993f-0715ec770933)

We'll be changing the attacker's post ID from the screenshot in step 2 to to the victim's post ID before clicking on the update button which will give us the privilege to edit the Victim's post.

![Update4](https://github.com/bugkay101/Web-Assessments/assets/149082141/d2997122-65d3-4702-8aa4-5369bcf198d5)

Let's change the caption from "Sweet Post" to "Edited Post"

After we click on the Update Post button, we should see the changes on the victim's profile.

![Update5](https://github.com/bugkay101/Web-Assessments/assets/149082141/60cfc742-d4ec-485e-b735-ca2270bec55d)

Now our victim's caption has been changed

