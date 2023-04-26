Unity内提供有API：``Random.insideUnitCircle``、``Random.insideUnitSphere``分别获得半径为1的圆形或者球形内的任意一个坐标点。

对于给定一个矩形面片若其没有旋转则可以很容易根据顶点坐标范围用两次随机数获得随机点。但若面片是更复杂的多边形或者有旋转则很难获得坐标。

下面提供一个根据Mesh范围获得其内部任意一点的方法：
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

//https://medium.com/@Scriptie/find-a-random-point-on-a-plane-using-barycentric-coordinates-in-unity-f9470945036b
public class FindRandomPointInPlane : MonoBehaviour
{

    List<Vector3> VerticeList = new List<Vector3>(); //List of local vertices on the plane
    List<Vector3> Corners = new List<Vector3>();
    public int sphereSize = 1;
    Vector3 RandomPoint;
    List<Vector3> EdgeVectors = new List<Vector3>();

    public Vector3 GetRandomPoint()
    {
        return this.RandomPoint;
    }
    //////void OnDrawGizmos()
    //////{
    //////    if (VerticeList.Count > 0)
    //////        for (int a = 0; a < Corners.Count; a++)
    //////        {
    //////            Gizmos.color = Color.yellow;
    //////            Gizmos.DrawSphere(Corners[a], sphereSize); //show coordinate as a sphere on editor
    //////        }

    //////    Gizmos.color = Color.magenta;
    //////    Gizmos.DrawSphere(RandomPoint, sphereSize); //show coordinate of the random point as a sphere on editor

    //////}


    // Start is called before the first frame update
    void Start()
    {
        VerticeList = new List<Vector3>(GetComponent<MeshFilter>().sharedMesh.vertices); //get vertice points from the mesh of the object
        CalculateCornerPoints();
    }

    //void Update()
    //{
    //CalculateCornerPoints(); //To show corner points with transform change
    //}

    void CalculateEdgeVectors(int VectorCorner)
    {
        EdgeVectors.Clear();

        EdgeVectors.Add(Corners[3] - Corners[VectorCorner]);
        EdgeVectors.Add(Corners[1] - Corners[VectorCorner]);
    }

    public void CalculateRandomPoint()
    {
        int randomCornerIdx = Random.Range(0, 2) == 0 ? 0 : 2; //there is two triangles in a plane, which tirangle contains the random point is chosen
                                                               //corner point is chosen for triangles as the variable
        CalculateEdgeVectors(randomCornerIdx); //in case of transform changes edge vectors change too

        float u = Random.Range(0.0f, 1.0f);
        float v = Random.Range(0.0f, 1.0f);

        if (v + u > 1) //sum of coordinates should be smaller than 1 for the point be inside the triangle
        {
            v = 1 - v;
            u = 1 - u;
        }
    
        RandomPoint = Corners[randomCornerIdx] + u * EdgeVectors[0] + v * EdgeVectors[1];
    }
    public void CalculateCornerPoints()
    {
        Corners.Clear(); //in case of transform changes corner points are reset

        Corners.Add(transform.TransformPoint(VerticeList[0])); //corner points are added to show  on the editor
        Corners.Add(transform.TransformPoint(VerticeList[10]));
        Corners.Add(transform.TransformPoint(VerticeList[110]));
        Corners.Add(transform.TransformPoint(VerticeList[120]));
    }
}

```