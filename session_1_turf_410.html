"""
Google Maps Distance Matrix Route Refiner
==========================================
Run this on your local machine to replace the Manhattan-distance walking estimates
with real Google Maps walking times and re-optimize the door order.

Requirements: pip install requests
Usage: python refine_routes_google.py

Cost: ~$79 against your $200/month free tier (for all 10 turfs in sessions 1-5)
"""

import json
import os
import math
import time
import requests

API_KEY = "AIzaSyDHqf8wuoCSWfco7PIdthHCrOengyOrsGQ"
BASE_URL = "https://maps.googleapis.com/maps/api/distancematrix/json"

def get_distance_matrix(origins, destinations):
    """Call Google Maps Distance Matrix API with walking mode."""
    origins_str = "|".join(f"{o['lat']},{o['lon']}" for o in origins)
    dests_str = "|".join(f"{d['lat']},{d['lon']}" for d in destinations)
    resp = requests.get(BASE_URL, params={
        'origins': origins_str,
        'destinations': dests_str,
        'mode': 'walking',
        'key': API_KEY,
    }, timeout=30)
    return resp.json()

def build_walking_matrix(addresses):
    """Build NxN walking duration matrix (seconds) via Google Maps API."""
    n = len(addresses)
    matrix = [[float('inf')]*n for _ in range(n)]
    for i in range(n):
        matrix[i][i] = 0

    chunk_size = 25
    n_chunks = math.ceil(n / chunk_size)
    total_requests = n_chunks * n_chunks
    req_count = 0

    for oi in range(n_chunks):
        o_s, o_e = oi*chunk_size, min((oi+1)*chunk_size, n)
        origins = [{'lat': addresses[i]['lat'], 'lon': addresses[i]['lon']} for i in range(o_s, o_e)]
        for di in range(n_chunks):
            d_s, d_e = di*chunk_size, min((di+1)*chunk_size, n)
            dests = [{'lat': addresses[j]['lat'], 'lon': addresses[j]['lon']} for j in range(d_s, d_e)]

            result = get_distance_matrix(origins, dests)
            req_count += 1
            print(f"    API request {req_count}/{total_requests}", end="\r")

            if result.get('status') != 'OK':
                print(f"\n  ERROR: {result.get('status')} — {result.get('error_message','')}")
                continue

            for ri, row in enumerate(result['rows']):
                for ci, elem in enumerate(row['elements']):
                    if elem['status'] == 'OK':
                        matrix[o_s+ri][d_s+ci] = elem['duration']['value']

            time.sleep(0.15)  # rate limit

    return matrix

def solve_tsp(addresses, dist):
    """Nearest-neighbor + 2-opt TSP solver."""
    n = len(addresses)
    if n <= 2:
        return list(range(n)), sum(dist[i][i+1] for i in range(n-1))

    best_tour, best_cost = None, float('inf')
    for start in range(min(n, 10)):
        visited = [False]*n
        tour = [start]; visited[start] = True; current = start; cost = 0
        for _ in range(n-1):
            nearest = min((j for j in range(n) if not visited[j]), key=lambda j: dist[current][j])
            cost += dist[current][nearest]
            tour.append(nearest); visited[nearest] = True; current = nearest
        if cost < best_cost:
            best_cost, best_tour = cost, tour

    # 2-opt
    improved = True
    while improved:
        improved = False
        for i in range(1, n-1):
            for j in range(i+1, n):
                old = dist[best_tour[i-1]][best_tour[i]] + dist[best_tour[j]][best_tour[(j+1)%n]]
                new = dist[best_tour[i-1]][best_tour[j]] + dist[best_tour[i]][best_tour[(j+1)%n]]
                if new < old - 0.01:
                    best_tour[i:j+1] = best_tour[i:j+1][::-1]
                    improved = True
    return best_tour, best_cost

def main():
    # Load routes file (from same directory)
    script_dir = os.path.dirname(os.path.abspath(__file__))
    routes_path = os.path.join(script_dir, '..', 'optimized_routes.json')

    # Try multiple locations
    for path in [routes_path, 'optimized_routes.json', os.path.join(script_dir, 'optimized_routes.json')]:
        if os.path.exists(path):
            routes_path = path
            break

    # If not found, check parent directory
    if not os.path.exists(routes_path):
        print("Could not find optimized_routes.json — place it in same folder as this script")
        return

    with open(routes_path) as f:
        routes = json.load(f)

    print(f"Found {len(routes)} turfs to optimize")
    total_elements = sum(len(r['ordered_addresses'])**2 for r in routes.values())
    print(f"Total API elements: {total_elements:,} (est. cost: ${total_elements*5/1000:.2f})")
    print()

    confirm = input("Proceed? (y/n): ").strip().lower()
    if confirm != 'y':
        print("Aborted.")
        return

    for tid, route in routes.items():
        addrs = route['ordered_addresses']
        n = len(addrs)
        print(f"\nTurf {tid}: {n} doors")
        print(f"  Calling Google Maps API ({n*n} elements)...")

        matrix = build_walking_matrix(addrs)
        inf_count = sum(1 for i in range(n) for j in range(n) if i!=j and matrix[i][j]==float('inf'))
        print(f"  {inf_count} unreachable pairs")

        tour, cost = solve_tsp(addrs, matrix)
        print(f"  Optimized route: {cost/60:.1f} min total walking")

        # Rebuild ordered addresses
        new_ordered = []
        for step, idx in enumerate(tour):
            a = addrs[idx]
            walk = 0 if step == 0 else matrix[tour[step-1]][idx]
            new_ordered.append({
                **a,
                'step': step + 1,
                'walk_from_prev_sec': round(walk),
                'walk_from_prev_min': round(walk/60, 1),
            })

        route['ordered_addresses'] = new_ordered
        route['total_walk_min'] = round(cost/60, 1)
        route['source'] = 'google_maps_api'

    # Save refined routes
    out_path = os.path.join(script_dir, 'optimized_routes_google.json')
    with open(out_path, 'w') as f:
        json.dump(routes, f, indent=2)
    print(f"\nSaved refined routes to {out_path}")
    print("Share this file in our next session and I'll regenerate the turf pages with real walking times.")

if __name__ == '__main__':
    main()
