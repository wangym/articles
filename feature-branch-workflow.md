Feature branch workflow  
=

1.Clone project:  
git clone git@example.com:project-name.git  

2.Create branch with your feature:  
git checkout -b $feature_name  

3.Write code. Commit changes:  
git commit -am "My feature is ready"  

4.Push your branch to GitLab:  
git push origin $feature_name  

5.Review your code on commits page.  

6.Create a merge request.  

7.Your team lead will review the code & merge it to the main branch.  

Check out, review, and merge locally
=

Step 1. Fetch and check out the branch for this merge request  
git fetch origin  
git checkout -b master origin/master  

Step 2. Review the changes locally  

Step 3. Merge the branch and fix any conflicts that come up  
git checkout {branch}  
git merge --no-ff master  

Step 4. Push the result of the merge to GitLab  
git push origin {branch}  
