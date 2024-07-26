async def test_change_page_category(  
    self, client, page_output, auth_headers, session  
):  
    create_category = {  
        "create": [  
            {  
                "data": {  
                    "title": "test_change_page_category1",  
                    "project_id": str(page_output["project"].id),  
                }  
            },  
            {  
                "data": {  
                    "title": "test_change_page_category2", 
                    "project_id": str(page_output["project2"].id),  
                }  
            },  
        ]  
    }  
    response = await client.post(  
        "/api/v1/category/handler/", headers=auth_headers, json=create_category  
    )  
    cat1_id = response.json()["created"][0]["id"]  
    cat2_id = response.json()["created"][1]["id"]  
    assert response.status_code == 201  
  
    pages_list = []  
    for cat in [cat1_id, cat2_id]:  
        for i in range(3):  
            create_page = {  
                "action": "+",  
                "data": {  
                    "action": "+",  
                    "title": "title_ou" + str(i),  
                    "project_id": str(page_output["project2"].id),  
                    "json_data": {"test": "output"},  
                    "category_id": cat,  
                },  
            }  
            response = await client.post(  
                "/api/v1/page/handler/", headers=auth_headers, json=create_page  
            )  
            pages_list.append(response.json()["created"]["id"])  
  
    new_orders = []  
    for page in pages_list[3:]:  
        update_page = {  
            "action": "~",  
            "data": {  
                "id": page,  
                "action": "~",  
                "title": "updated",  
                "json_data": {"test": "updated"},  
                "category_id": cat1_id,  
            },  
        }  
        response = await client.post(  
            "/api/v1/page/handler/", headers=auth_headers, json=update_page  
        )  
        new_orders.append(response.json()["updated"]["order"])  
        assert response.json()["updated"]["category_id"] == cat1_id  
    assert new_orders == [3, 4, 5]

@staticmethod  
async def _get_last_order_num(pages: list[Page]) -> int:  
    if not pages:  
        return 0  
    return pages[-1].order + 1

if obj.category_id and obj.category_id != old_page.category_id:  
    pages = await self.repository.get_unique_pages(  
        "category_id", obj.category_id  
    )  
    pages.sort(key=lambda p: p.order)  
    new_order_num = await self._get_last_order_num(pages)  
    page.order = new_order_num

"project2": project2,