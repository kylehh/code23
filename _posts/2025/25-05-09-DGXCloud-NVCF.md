---
title: K8S behind DGXCloud and NVCF
mathjax: true
toc: true
categories:
  - Study
tags:
  - K8S
---

Recently all work seems K8S related and practices around k8s helped me onboard DGXCloud and NVCF Helm deployment really fast. 
## 0 Web Server
It's totally irrelavent but some information about web sever
- WSGI: The old generation, for Web Sever Gateway Interface. It uses framework like **Flask** and **Djando**. and servers with names I never heard of.
- ASGI: The new generation and A stands for Async. **FastAPI** is such framework and **uvicorn** is the webserver application. 

## 1 DGXCloud
1. Run:ai CLI and k8s setup following steps [here](https://docs.nvidia.com/dgx-cloud/run-ai/latest/advanced.html#setting-up-your-kubernetes-configuration-file)
2. Run `runai login` and authenicate through OSS
3. Run `runai kubeconfig set` to get token set
4. Then it's like a normal cluster operation with a specific namespace.It's not shown up in DGXC UI due to not in runai schedular. 
5. Will add more details 

## 2 NVCF
NVCF can be considered as serverless k8s, and bring your own cluster approach would make the deployment much more transparent.
1. Register cluster with NVCF.  
I haven't tested out this step but worked with a registerd cluster
2. Health Check implementation in code.   
All is needed is a health check. 

```python
app = FastAPI()
class HealthCheck(BaseModel):
    status: str = "OK"

@app.get("/health", tags=["healthcheck"],summary="Perform a Health Check", response_description="Return HTTP Status Code 200 (OK)", status_code=status.HTTP_200_OK,
response_model=HealthCheck)
def get_health() -> HealthCheck:
    return HealthCheck(status="OK") 

```  

2. Helm chart preparation  
A service will be exposed in NVCF, and will have `/health` and `/generate` two endpoint implemented in the Pod behind it.  

```sh
helm template chart/
helm generate chart/
# Will generate chart_name-version.tgz based on Chart.yaml
ngc registry chart create ngc_org_id/chart_name --short-desc "chart des"
ngc registry chart push ngc_org_id/chart_name:version --dry-run
```  

3. NVCF Deployment  
Set the **service** for exposing ports, and **endpoints** for health check and inference during function creations.  

```sh
# Deploy to the on-prem cluster
ngc cf fn deploy create function_id:version_id --targeted-deployment-specification "A100:ON-PREM.GPU.A100_8x:1:1:1:machinename-a100x8"
```  
