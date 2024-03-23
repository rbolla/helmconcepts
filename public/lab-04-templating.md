# Lab 04: Templating

## A. Understand template and values.yaml overrides

1. In a new/empty directory create a new helm chart.

```
helm create myapptemplate
```

2. Change into the myapptemplate directory.

```
cd myapptemplate/
```

3. Inspect **values.yaml**. Which version of nginx will be deployed?

```
cat values.yaml
```

4. Package and deploy the helm chart as is.

```
cd ../
```
```
helm package myapptemplate
```
```
helm install myapptemplate myapptemplate --version 0.1.0
```

5. Check what image your deployed application is using. Is it what you expect?

```
kubectl describe pods | grep Image
```

6. Navigate to **myapptemplates/templates/**

```
cd myapptemplates/templates
```

7. Inspect **deployment.yaml**. Which line(s) specify the Docker image used in the deployment?

```
cat -n deployment.yaml
```

8. Make the following modification to **deployment.yaml**. Do you think this is a good change?

```
sed -i 's/{{ .Values.image.tag | default .Chart.AppVersion }}/1.24.0/g' deployment.yaml
```
```
cat deployment.yaml | grep -n image
```

9. Increment the chart version in **Chart.yaml**.

```
cd ../
```
```
sed -i 's/version: 0.1.0/version: 0.2.0/g' Chart.yaml
```
```
cat -n Chart.yaml
```

10. Check for any syntax errors.

```
helm lint .
```

11. Package a new version of the chart.

```
cd ../
```
```
helm package myapptemplate
```

12. Upgrade the existing release with the new chart version.

```
helm upgrade myapptemplate myapptemplate --version 0.2.0
```

13. Check the image version in the new release. What has changed?

```
kubectl describe pods | grep Image
```

14. Change back into the chart directory and change the image tag in **values.yaml**.

```
cd myapptemplate/
```
```
sed -i 's/tag: ""/tag: 1.25.0/g' values.yaml
```
```
cat values.yaml | grep -n tag:
```

15. Increment the chart version in **Chart.yaml**.

```
sed -i 's/version: 0.2.0/version: 0.3.0/g' Chart.yaml
```
```
cat Chart.yaml | grep -n version
```

16. Check for any syntax errors.

```
helm lint .
```

17. Package a new version of the chart.

```
cd ../
```
```
helm package myapptemplate
```

18. Upgrade the existing release with the new chart version.

```
helm upgrade myapptemplate myapptemplate --version 0.3.0
```
19. Check the image version in the new release. 

```
kubectl describe pods | grep Image
```

How has it changed? What does this tell you about the precedence of override values in Helm charts? In which of **Chart.yaml**, **values.yaml**, or **deployment.yaml** should we have set the tag?

20. Navigate into **myapptemplates/templates**.

```
cd myapptemplates/templates
```

21. Restore **deployment.yaml** back to its previous configuration

```
sed -i 's/1.24.0/{{ .Values.image.tag | default .Chart.AppVersion }}/g' deployment.yaml
```
```
cat deployment.yaml | grep -n tag
```

22. Increment chart version and check for errors.
```
cd ../
```
```
sed -i 's/version: 0.3.0/version: 0.4.0/g' Chart.yaml
```
```
cat Chart.yaml | grep -n version
```
```
helm lint .
```

23. Package and deploy the updated chart.
```
cd ../
```
```
helm package myapptemplate
```
```
helm upgrade myapptemplate myapptemplate --version 0.4.0
```

24. Check the image tag in the updated deployment. Where is the value used coming from?

```
kubectl describe pods | grep Image
```

25. Finally, update the chart, this time with a value passed in at install.

```
helm upgrade myapptemplate myapptemplate --version 0.4.0 --set image.repository=nginx,image.tag=1.25.4
```
```
kubectl describe pods | grep Image
```